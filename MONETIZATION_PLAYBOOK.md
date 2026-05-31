# Fingofy — Monetizasyon ve Play Satıcı Hesabı Playbook

Bu doküman gelir genişletme planının operasyon ve teknik referansıdır.

**IAP ürün ID (kod ile aynı):** `remove_ads_lifetime`

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

---

## 3. License tester ile IAP test

### Console

1. **Settings → License testing**
2. Test Gmail hesabınızı ekleyin
3. `remove_ads_lifetime` ürünü **Active** olmalı

### Cihaz

1. Test hesabıyla Play Store’a giriş
2. Kapalı test veya Production’dan **1.1.18+** (versionCode 22+) kurulum
3. Profil → **Reklamları kaldır** → satın al
4. Test kartı ile ödeme (license tester’da gerçek ücret alınmaz)
5. Dashboard / işlem listesinde banner kayboldu mu?
6. **Satın alımları geri yükle** — uygulamayı sil-yükle sonrası test

### Sorun giderme

| Sorun | Çözüm |
|-------|--------|
| Ürün bulunamadı | Product ID eşleşmesi, Active durumu, 2–4 saat bekleme |
| Ödeme profili yok | Bölüm 1’i tamamlayın |
| Satın alma iptal | Normal; kullanıcı iptal etmiş |
| Reklamlar hâlâ görünüyor | Firestore `entitlements.adsRemoved`, uygulamayı yeniden başlat |

---

## 4. Production release (IAP’li build)

1. `version.json` → 1.1.18 / versionCode 22 (veya üzeri)
2. `npm run check:android-16kb`
3. `.\gradlew.bat clean bundleRelease`
4. Play Console → **Production** → yeni AAB
5. Sürüm notları: [play-store-release-notes-tr.txt](../store-assets/metadata/play-store-release-notes-tr.txt)
6. Rollout %100

---

## 5. Data safety ve gizlilik (Console)

Play Console → **App content → Data safety**:

- [ ] **Purchase history** — Evet (Google Play Billing)
- [ ] Veri şifreleme / aktarım — mevcut beyanla uyumlu
- [ ] Gizlilik politikasında satın alma maddesi — [DATA_SAFETY_IAP_NOTES.md](./DATA_SAFETY_IAP_NOTES.md)

---

## 6. Teknik referans (geliştiriciler)

| Dosya | Açıklama |
|-------|----------|
| [constants/iapProducts.ts](../constants/iapProducts.ts) | Ürün ID |
| [services/purchaseService.ts](../services/purchaseService.ts) | Play Billing |
| [contexts/EntitlementsContext.tsx](../contexts/EntitlementsContext.tsx) | `adsRemoved` state |
| [components/ads/BannerAd.tsx](../components/ads/BannerAd.tsx) | Reklam gating |

Yerel doğrulama: `npm run verify:iap-build`

---

## 7. Gelir kanalları özeti

| Kanal | Durum |
|-------|--------|
| AdMob banner | Aktif (adsRemoved=false iken) |
| IAP remove_ads_lifetime | Bu sürümle |
| Premium abonelik | 2. faz (henüz yok) |
