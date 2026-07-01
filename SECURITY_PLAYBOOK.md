# Fingofy Güvenlik Playbook

Bu doküman güvenlik test checklist'i, Firebase Console ayarları, anahtar yönetimi ve olay müdahale adımlarını içerir.

## Mimari özeti

- **Kimlik doğrulama:** Firebase Auth
- **Veri:** Firestore (kullanıcı izolasyonu + alan düzeyinde kurallar)
- **Profil fotoğrafları:** Firebase Storage (`profilePictures/{uid}/avatar.webp`); Firestore'da yalnızca path string
- **Koruma katmanları:** App Check (Play Integrity), Firestore Rules, Storage Rules, Cloud Functions
- **IAP:** `verifyPlayPurchase` — Play Developer API ile sunucu doğrulaması
- **Güvenlik logları:** `logSecurityEvent` Cloud Function (istemci doğrudan yazamaz)

## Release öncesi checklist

### Firebase Console

- [ ] **App Check → Firestore:** Enforcement = **Enforced**
- [ ] **App Check → Storage:** Enforcement = **Enforced** (profil fotoğrafları)
- [ ] **App Check → Authentication:** Enforcement = **Enforced** (önerilir)
- [ ] **App Check → Cloud Functions:** Enforcement = **Enforced**
- [ ] Debug token'lar yalnızca geliştirici cihazlarda; production token rotate edildi
- [ ] Play Console → API access → Service account, Play Developer API yetkili
- [ ] Cloud Functions deploy: `verifyPlayPurchase`, `logSecurityEvent`, `verifyPlayIntegrityToken`

### Kod / CI

- [ ] `npm run test:rules` yeşil
- [ ] `npm run functions:build` yeşil
- [ ] `npm audit` kritik açık yok
- [ ] Repoda `google-services.json`, keystore, debug App Check token yok

### Manuel test (staging / internal track)

| # | Senaryo | Beklenen |
|---|---------|----------|
| A2 | Firestore'da `entitlements.adsRemoved: true` yaz | **Red** |
| B1 | Modifiye client ile entitlement yazma | **Etkisiz** |
| B2 | Sahte purchaseToken Function'a gönder | **400/500** |
| B4 | Restore — Play'de aktif olmayan ürün | Entitlement yok |
| C3 | Release APK + user CA MITM | Bağlantı reddedilir |
| D1 | Integrity endpoint 30+ req/dk | **429** |

## Firestore kuralları — önemli kısıtlar

- `users.entitlements`: istemci yazamaz (yalnızca `verifyPlayPurchase` Admin SDK)
- `users.profilePicture`: yalnızca `builtin:{id}` veya kendi `upload:profilePictures/{uid}/avatar.*` path'i
- Tüm owner koleksiyonlarda `userId` güncellemede değiştirilemez
- `analytics`: okuma kapalı
- `securityLogs`, `purchaseVerifications`, `_rateLimits`: istemci erişimi kapalı
- `appLogs`: alan validasyonu zorunlu
- `fcmQueue`: payload validasyonu + Function tarafında saatlik rate limit

## Storage kuralları

Path: `profilePictures/{userId}/avatar.{webp|jpg|jpeg|png}`

- **read:** oturum açmış kullanıcılar
- **write/delete:** yalnızca `request.auth.uid == userId`, max 2 MB, `image/(webp|jpeg|png)`

Deploy: `firebase deploy --only storage` (veya `npm run deploy:security` ile birlikte)

iOS galeri izni: `NSPhotoLibraryUsageDescription` (`Info.plist` — release build öncesi doğrula)

## Anahtar ve sır yönetimi

| Varlık | Nerede | Not |
|--------|--------|-----|
| Upload keystore | Lokal, `.gitignore` | Yedek güvenli depoda |
| `google-services.json` | Lokal / CI secret | APK'da client config normal |
| App Check debug token | `FIREBASE_APP_CHECK_DEBUG_TOKEN` env | Repoya commit etme |
| Play service account | GCP / Play Console | Functions default credentials |

### Token rotate prosedürü

1. Firebase Console → App Check → Manage debug tokens → eski token sil
2. Yeni token oluştur veya Logcat'ten al
3. `.env` / CI secret güncelle
4. Ekip üyelerine bildir

## Cloud Functions rate limitleri

| Function | Limit |
|----------|-------|
| `verifyPlayIntegrityToken` | 30/dk / uid |
| `verifyPlayPurchase` | 10/dk / uid |
| `logSecurityEvent` | 30/dk / uid |
| `processFcmQueueOnCreate` | 20/saat / uid |

## IAP akışı

```
Kullanıcı satın alır → Play Billing token
  → verifyPlayPurchase (Auth + Play API)
  → purchaseVerifications kaydı
  → users.entitlements (Admin SDK)
  → Uygulama profili yeniler → reklamlar kapanır
```

## PIN güvenliği

- PIN bcrypt hash ile `EncryptedStorage`'da saklanır
- 5 başarısız deneme → 5 dakika kilit
- Legacy düz metin PIN otomatik migrate edilir

## Ağ güvenliği (Android)

- **Release:** yalnızca sistem CA (`network_security_config.xml`)
- **Debug:** user CA izinli (mitmproxy geliştirme için)

## Olay müdahale (incident response)

1. **Şüpheli entitlement bypass:** `purchaseVerifications` ve `users.entitlements` audit
2. **Firestore spam:** Usage dashboard, `appLogs` / `fcmQueue` yazım grafikleri
3. **API abuse:** Cloud Functions logları, `_rateLimits` koleksiyonu
4. **Sızıntı şüphesi:** App Check token rotate, kuralları geçici sıkılaştır
5. Kullanıcı bildirimi (KVKK/GDPR gerekiyorsa)

## Tersine mühendislik notları

- Firebase API key APK'da görünür — beklenen davranış
- Hermes bundle analiz edilebilir — sunucu sırları client'ta tutulmamalı
- ProGuard release'te aktif; tam gizlilik garantisi yok

## Komutlar

```bash
npm run test:rules          # Firestore kuralları
npm run functions:build     # Cloud Functions derleme
firebase deploy --only firestore:rules,functions
```

## İlgili dosyalar

- [`firestore.rules`](../firestore.rules)
- [`functions/src/purchase.ts`](../functions/src/purchase.ts)
- [`functions/src/security.ts`](../functions/src/security.ts)
- [`functions/src/rateLimit.ts`](../functions/src/rateLimit.ts)
- [`services/purchaseVerificationService.ts`](../services/purchaseVerificationService.ts)
- [`config/firebase.ts`](../config/firebase.ts)
