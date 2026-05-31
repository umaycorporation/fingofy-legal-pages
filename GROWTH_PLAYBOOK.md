# Fingofy — Üretim Sonrası Büyüme Playbook (90 gün)

Bu doküman production yayını sonrası operasyon, ASO, kullanıcı edinimi ve Play yorumları için tek referanstır.

## Hızlı bağlantılar

| Kaynak | URL |
|--------|-----|
| Play mağaza listesi | https://play.google.com/store/apps/details?id=com.umaycorporation.fingofy |
| Play Console | https://play.google.com/console |
| AdMob | https://apps.admob.com/ |
| app-ads.txt | https://umaycorporation.github.io/app-ads.txt |
| Geliştirici sitesi | https://umaycorporation.github.io/fingofy-legal-pages/ |

### Ücretsiz edinim (45 gün)

**Ana checklist:** [FREE_USER_ACQUISITION_CHECKLIST.md](./FREE_USER_ACQUISITION_CHECKLIST.md)

| Rehber | İçerik |
|--------|--------|
| [SOCIAL_CONTENT_CALENDAR.md](./SOCIAL_CONTENT_CALENDAR.md) | 4 haftalık Instagram / TikTok metinleri |
| [COMMUNITY_ORGANIC_GUIDE.md](./COMMUNITY_ORGANIC_GUIDE.md) | Forum ve WhatsApp paylaşımı |
| [REVIEW_REQUEST_TEMPLATES.md](./REVIEW_REQUEST_TEMPLATES.md) | Play yorum isteme ve yanıt şablonları |

Yerel doğrulama: `npm run verify:growth-build`

### Monetizasyon (IAP)

**Playbook:** [MONETIZATION_PLAYBOOK.md](./MONETIZATION_PLAYBOOK.md) | `npm run verify:iap-build`

---

## 1. Play yorumları neden görünmüyor? (Kontrol listesi)

Uygulama içinde kullanıcılar birbirinin yorumunu **görmez**; yorumlar yalnızca **Google Play mağaza sayfasında** herkese açıktır.

### Console’da var, mağaza sayfasında yok

Production yorumları Console’da görünüp mağazada çıkmıyorsa ayrıntılı teşhis:

**→ [PLAY_STORE_REVIEWS_TROUBLESHOOTING.md](./PLAY_STORE_REVIEWS_TROUBLESHOOTING.md)**

Özet nedenler: moderasyon gecikmesi (24–72 saat), geliştirici/license test hesabı, Play dışı kurulum, gizli geri bildirim (private feedback).

### Play Console kontrolü (haftada 1)

- [ ] **Grow → Store presence → Ratings and reviews** → filtre: **Production**
- [ ] Test geri bildirimleri ile karıştırmayın: **Testing → Feedback** mağazada görünmez
- [ ] Mağaza URL’yi giriş yapmadan açın ve **Değerlendirmeler** bölümünü kontrol edin
- [ ] Yorum bırakan kullanıcı uygulamayı **Play Store’dan** mı kurdu? (sideload / test APK sayılmaz)
- [ ] Production yayını **%100** mü? (Release → Production)
- [ ] İlk yorumlar için **24–48 saat** bekleyin (moderasyon gecikmesi)
- [ ] Console vs mağaza uyumsuzluğu: [PLAY_STORE_REVIEWS_TROUBLESHOOTING.md](./PLAY_STORE_REVIEWS_TROUBLESHOOTING.md)

### Ürün tarafı (bu repoda yapıldı)

- Profil menüsü ve Yardım ekranında **Uygulamayı değerlendir** → `react-native-in-app-review`
- **Uygulamayı paylaş** → Play listesi linki
- Dashboard **paylaşım banner** (ilk bütçe sonrası, bir kerelik) → [ShareAppBanner.tsx](../components/growth/ShareAppBanner.tsx)
- İlk bütçe / işlem sonrası otomatik in-app review istemi (3+ gün, 90 gün cooldown)

---

## 2. Haftalık izleme rutini (Hafta 1+)

Her **Pazartesi** (15–20 dk):

| Alan | Nerede | Eşik / aksiyon |
|------|--------|----------------|
| Kilitlenme / ANR | Play Console → Android vitals | Kritik artış → hotfix |
| Çökme detayı | Sentry (production DSN) | Yeni regresyon → önceliklendir |
| 16 KB uyumluluk | App Bundle Explorer → Memory page size | Desteklenmiyorsa → `npm run check:android-16kb` |
| AdMob | AdMob paneli | Politika uyarısı / düşük fill rate |
| app-ads.txt | Tarayıcıda kök URL | 404 ise acil düzelt |
| Firebase funnel | Analytics → Events: `first_open`, `sign_up`, `first_transaction`, `budget_created`, `app_share_completed` | Düşük dönüşüm → UX incele |
| Yorumlar | Ratings and reviews | 48 saat içinde yanıt |

Release öncesi: [PLAY_CONSOLE_ANDROID_COMPLIANCE.md](./PLAY_CONSOLE_ANDROID_COMPLIANCE.md) + `npm run check:android-16kb`.

---

## 3. ASO (mağaza optimizasyonu)

Kaynak metinler: [store-assets/metadata/](../store-assets/metadata/).

### Güncellenecekler (Play Console → Main store listing)

- [ ] **Kısa açıklama** (80 karakter) — `play-store-description.txt` başındaki satır
- [ ] **Uzun açıklama** — özellik listesi güncel mi?
- [ ] **Ekran görüntüleri** — 6–8 adet, sıra: Dashboard → işlemler → bütçe → hedef → bildirim
- [ ] **Feature graphic** — tek cümle değer önerisi
- [ ] **Sürüm notları** — `play-store-release-notes-tr.txt` / `-en.txt`
- [ ] **Store listing experiments** — kısa açıklama veya screenshot A/B (varsa)

### Anahtar kelimeler (referans)

`store-assets/metadata/keywords.txt` — Play’de ayrı alan yoksa açıklamaya doğal şekilde yedirin.

---

## 4. Kullanıcı edinimi (0 → 1000)

### Organik (öncelik)

1. ASO güncellemeleri (yukarı)
2. Kısa video / ekran kaydı (bütçe kurulumu, hedef birikimi)
3. Topluluk paylaşımı (spam değil; gerçek değer + Play linki)
4. Uygulama içi **Paylaş** (Profil, Yardım, Dashboard banner)

Detay: [FREE_USER_ACQUISITION_CHECKLIST.md](./FREE_USER_ACQUISITION_CHECKLIST.md), [SOCIAL_CONTENT_CALENDAR.md](./SOCIAL_CONTENT_CALENDAR.md)

### Ücretli (isteğe bağlı)

- Google App Campaigns (UAC), günlük düşük bütçe
- AdMob geliri ile edinim maliyetini karşılaştırın

### KPI tablosu (haftalık)

| Metrik | Kaynak |
|--------|--------|
| Kurulumlar | Play Console → Acquisition |
| MAU / DAU | Firebase Analytics |
| sign_up → first_transaction | Analytics funnel |
| Ortalama puan | Ratings and reviews |
| Kilitlenme oranı | Android vitals |

---

## 5. 90 günlük fazlar

### Faz 1 (Gün 0–14): Stabilite ve mağaza

- Vitals + Sentry
- Play yorum kontrol listesi (Bölüm 1)
- ASO screenshot + kısa açıklama
- İlk kullanıcılardan Play üzerinden değerlendirme

### Faz 2 (Gün 15–45): Dönüşüm

- In-app review + paylaşım (kodda mevcut)
- Firebase funnel event’lerini izle
- Yorumlara 48 saat içinde yanıt

### Faz 3 (Gün 46–90): Ölçek

- UAC (küçük bütçe)
- İçerik takvimi (ayda 2 video)
- En çok istenen 1–2 ürün özelliği

---

## 6. Teknik referans (geliştiriciler)

| Dosya | Açıklama |
|-------|----------|
| [constants/storeLinks.ts](../constants/storeLinks.ts) | Play URL’leri |
| [services/storeEngagementService.ts](../services/storeEngagementService.ts) | Review + paylaşım |
| [services/growthFunnelService.ts](../services/growthFunnelService.ts) | `first_open`, `sign_up`, `first_transaction`, `budget_created` |
| [components/profile/ProfileMenu.tsx](../components/profile/ProfileMenu.tsx) | Değerlendir / paylaş menüsü |
| [components/growth/ShareAppBanner.tsx](../components/growth/ShareAppBanner.tsx) | Dashboard paylaşım banner |
| [services/sharePromptService.ts](../services/sharePromptService.ts) | Banner gösterim koşulu |
| [docs/FREE_USER_ACQUISITION_CHECKLIST.md](./FREE_USER_ACQUISITION_CHECKLIST.md) | 45 günlük ücretsiz edinim checklist |

Firebase’de özel dönüşüm hunisi oluşturmak için önerilen adımlar: `first_open` → `sign_up` → `budget_created` → `first_transaction`.
