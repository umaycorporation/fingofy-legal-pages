# Fingofy — Monetizasyon ve Play Satıcı Hesabı Playbook

Bu doküman gelir genişletme planının operasyon ve teknik referansıdır.

**IAP ürün ID (kod ile aynı):**

| Tür | Product ID |
|-----|------------|
| Tek seferlik (legacy) | `remove_ads_lifetime` |
| Abonelik (aylık) | `fingofy_premium_monthly` |
| Abonelik (yıllık) | `fingofy_premium_yearly` |

**Play linki:** https://play.google.com/store/apps/details?id=com.umaycorporation.fingofy

---

## 1. Bireysel ödeme profili (Play Console — siz)

### Ön hazırlık

- T.C. kimlik ve adres (Play profili ile aynı)
- TCKN (vergi)
- TL banka hesabı IBAN (hesap sahibi = Play yasal adınız)
- Telefon doğrulaması

### Adımlar

1. https://play.google.com/console → giriş
2. **Monetize with Play** veya **Settings → Developer account → Payments profile**
3. **Set up payments profile** → **Individual / Bireysel**
4. Yasal ad-soyad, Türkiye adresi, iletişim
5. **Tax information** → TCKN + Google’ın istediği vergi formu
6. **Payment methods** → IBAN ekle, doğrulama tamamlansın
7. Durum **Verified / Onaylandı** olana kadar bekleyin (saatler – günler)

Kontrol: **Monetize → Products** menüsü açılıyor mu?

---

## 2. In-app product oluşturma

1. **Monetize → Products → In-app products**
2. Uygulama: **Fingofy** (`com.umaycorporation.fingofy`)
3. **Create product**
4. **Product ID:** `remove_ads_lifetime` (değiştirmeyin)
5. **Name:** Reklamları kaldır
6. **Description:** Fingofy’de tüm banner reklamlarını kalıcı olarak kaldırır.
7. **Price:** Türkiye fiyat şablonu (ör. 39,99 TL — siz belirleyin)
8. **Save** → **Activate**

> **Not:** `remove_ads_lifetime` eski alıcılar için korunur. Yeni kullanıcılar **Profil → Fingofy Premium** aboneliğine yönlendirilir; profil menüsünde ayrı “Reklamları kaldır” satırı yoktur.

---

## 3. Abonelik ürünleri oluşturma (Premium)

Kod [`constants/iapProducts.ts`](../constants/iapProducts.ts) iki abonelik SKU’su bekler. **In-app products** değil, **Subscriptions** menüsünü kullanın.

### Ön koşul

Bölüm 1’deki ödeme profili **Verified** olmalı; **Monetize → Products → Subscriptions** menüsü açılmalı.

### Aylık abonelik

1. **Monetize → Products → Subscriptions** → **Create subscription**
2. Uygulama: **Fingofy** (`com.umaycorporation.fingofy`)
3. **Subscription ID:** `fingofy_premium_monthly` (değiştirmeyin)
4. **Name:** Fingofy Premium (Aylık)
5. **Description:** Reklamsız deneyim ve Premium özellikler
6. **Base plan** ekle: faturalama dönemi **Monthly**, fiyat (ör. TR şablonu)
7. **Save** → **Activate**

### Yıllık abonelik

1. Yeni abonelik oluştur
2. **Subscription ID:** `fingofy_premium_yearly`
3. **Name:** Fingofy Premium (Yıllık)
4. **Base plan:** **Yearly**, fiyat (ör. aylığın ~10 ayı — uygulamada %20 tasarruf metni var)
5. **Save** → **Activate**

### Console önerileri (her abonelik)

- **Grace period / Account hold:** varsayılan veya 3 gün grace
- **Resubscribe:** açık
- **Pricing:** en az Türkiye + hedef ülkeler

### Yayın öncesi Console içerik

1. **Monetize → Monetization setup** — abonelik avantajları (reklamsız, export vb.)
2. **App content → Data safety** — Purchase history: Evet ([DATA_SAFETY_IAP_NOTES.md](./DATA_SAFETY_IAP_NOTES.md))
3. Gizlilik politikasında abonelik / iptal / iade maddesi

### Bilinen kod eksikliği (production abonelik)

[`functions/src/purchase.ts`](../functions/src/purchase.ts) şu an yalnızca `remove_ads_lifetime` doğrular. [`purchaseService.ts`](../services/purchaseService.ts) abonelik için sunucu doğrulaması olmadan yerel cache yazar (optimistic). **Mağaza yayını öncesi** sunucu tarafı abonelik doğrulaması (`purchases.subscriptionsv2.get`, Firestore `entitlements.premium`) ayrı görev olarak tamamlanmalıdır.

---

## 4. License tester ile IAP test

### Console

1. **Settings → License testing**
2. Test Gmail hesabınızı ekleyin
3. `remove_ads_lifetime` ve abonelik SKU’ları (`fingofy_premium_monthly`, `fingofy_premium_yearly`) **Active** olmalı

### Cihaz

1. Test hesabıyla Play Store’a giriş
2. **Internal testing** veya **Closed testing** kanalından test hesabıyla kurulum
3. Profil → **Fingofy Premium** → aylık veya yıllık satın al
4. Test kartı ile ödeme (license tester’da gerçek ücret alınmaz)
5. Dashboard / işlem listesinde banner kayboldu mu? Premium rozeti görünüyor mu?
6. **Satın alımları geri yükle** — uygulamayı sil-yükle sonrası test
7. (Legacy) Eski `remove_ads_lifetime` alıcıları **geri yükle** ile `adsRemoved` entitlement almaya devam eder

### Sorun giderme

| Sorun | Çözüm |
|-------|--------|
| Billing yapılandırılmadı (debug APK) | Uygulamayı Play Internal/Closed testing kanalından kurun; `npm run android` ile debug APK'da billing çalışmaz |
| Ürün bulunamadı | Product ID eşleşmesi, Active durumu, 2–4 saat bekleme |
| Ödeme profili yok | Bölüm 1’i tamamlayın |
| Satın alma iptal | Normal; kullanıcı iptal etmiş |
| Reklamlar hâlâ görünüyor | `entitlements.premium` veya `entitlements.adsRemoved`; uygulamayı yeniden başlat |

---

## 5. Production release (IAP’li build)

1. `version.json` → 1.1.18 / versionCode 22 (veya üzeri)
2. `npm run check:android-16kb`
3. `.\gradlew.bat clean bundleRelease`
4. Play Console → **Production** → yeni AAB
5. Sürüm notları: [play-store-release-notes-tr.txt](../store-assets/metadata/play-store-release-notes-tr.txt)
6. Rollout %100

---

## 6. Data safety ve gizlilik (Console)

Play Console → **App content → Data safety**:

- [ ] **Purchase history** — Evet (Google Play Billing)
- [ ] Veri şifreleme / aktarım — mevcut beyanla uyumlu
- [ ] Gizlilik politikasında satın alma maddesi — [DATA_SAFETY_IAP_NOTES.md](./DATA_SAFETY_IAP_NOTES.md)

---

## 7. Teknik referans (geliştiriciler)

| Dosya | Açıklama |
|-------|----------|
| [constants/iapProducts.ts](../constants/iapProducts.ts) | Ürün ID |
| [services/purchaseService.ts](../services/purchaseService.ts) | Play Billing |
| [contexts/EntitlementsContext.tsx](../contexts/EntitlementsContext.tsx) | `premium`, `adsRemoved` state |
| [components/ads/BannerAd.tsx](../components/ads/BannerAd.tsx) | Reklam gating (`premium \|\| adsRemoved`) |

Yerel doğrulama: `npm run verify:iap-build`

---

## 8. Gelir kanalları özeti

| Kanal | Durum |
|-------|--------|
| AdMob banner | Aktif (`premium` ve `adsRemoved` false iken) |
| IAP remove_ads_lifetime | Legacy — geri yükleme desteklenir |
| Premium abonelik | Console kurulumu (Bölüm 3); sunucu doğrulaması sonraki faz |
