# Fingofy — Ücretsiz Kullanıcı Edinimi Kontrol Listesi (45 gün)

Bu doküman [GROWTH_PLAYBOOK.md](./GROWTH_PLAYBOOK.md) ile birlikte kullanılır. Play Console adımları **sizin tarafınızda** yapılır; repo tarafında growth kodu ve ASO metinleri hazırdır.

**Play linki (tüm paylaşımlarda):**  
https://play.google.com/store/apps/details?id=com.umaycorporation.fingofy

---

## 1. Production growth build doğrulama

Growth özellikleri **v1.1.17+** (versionCode **21**) ile gelir.

### Kod tarafı (repoda)

| Özellik | Dosya |
|---------|--------|
| In-app review + paylaşım | [services/storeEngagementService.ts](../services/storeEngagementService.ts) |
| Funnel olayları | [services/growthFunnelService.ts](../services/growthFunnelService.ts) |
| Profil menüsü | [components/profile/ProfileMenu.tsx](../components/profile/ProfileMenu.tsx) |
| Dashboard paylaşım banner | [components/growth/ShareAppBanner.tsx](../components/growth/ShareAppBanner.tsx) |

Yerel doğrulama:

```powershell
npm run verify:growth-build
npm run check:android-16kb
```

### Play Console (siz)

- [ ] **Release → Production** → aktif sürüm **1.1.17** (veya üzeri), versionCode **≥ 21**
- [ ] Rollout **%100**
- [ ] **Countries/regions** → Türkiye dahil hedef pazarlar açık
- [ ] Test cihazında Play Store’dan kurulum → Profil’de **Uygulamayı paylaş** ve **Değerlendir** görünüyor mu?
- [ ] İlk bütçe veya işlem sonrası (3+ gün) in-app review tetikleniyor mu?

---

## 2. Test kanalları temizliği

- [ ] **Release → Testing → Open testing** → aktif release **Durdur** (Production tam yayında ise)
- [ ] **Closed testing** → yalnızca QA ekibi kalsın; Production ile aynı veya daha yeni sürüm
- [ ] Açık test / kapalı test **linklerini** tanıtımda paylaşmayın
- [ ] License tester listesinde olmayan gerçek kullanıcılar Play’den kurulum yapsın (yorumlar için)

---

## 3. ASO — mağaza listesi

Kaynak metinler: [store-assets/metadata/](../store-assets/metadata/)

Play Console → **Grow → Store presence → Main store listing**

- [ ] **Kısa açıklama** (80 karakter) — `play-store-description.txt` satır 4
- [ ] **Uzun açıklama** — aynı dosya, güncel özellik listesi
- [ ] **6–8 ekran görüntüsü** — sıra: Dashboard → işlemler → bütçe → hedef → plan → bildirim
- [ ] **Feature graphic** (1024×500)
- [ ] **Sürüm notları** — `play-store-release-notes-tr.txt` / `-en.txt`
- [ ] Anahtar kelimeler: [keywords.txt](../store-assets/metadata/keywords.txt) (açıklamaya doğal yedirin)
- [ ] Varsa **Store listing experiments** — kısa açıklama veya 1. screenshot A/B

Detay: [store-assets/DEPLOYMENT_CHECKLIST.md](../store-assets/DEPLOYMENT_CHECKLIST.md)

---

## 4. Yorum ve güven

- [ ] 5–10 kişiden **Play Store üzerinden kurulum** + 5 yıldız + kısa yorum iste
- [ ] Test APK, internal/open/closed test, license tester hesabı **sayılmaz** → [PLAY_STORE_REVIEWS_TROUBLESHOOTING.md](./PLAY_STORE_REVIEWS_TROUBLESHOOTING.md)
- [ ] Console yorumlarına **48 saat içinde** yanıt
- [ ] Hazır mesajlar: [REVIEW_REQUEST_TEMPLATES.md](./REVIEW_REQUEST_TEMPLATES.md)
- [ ] Marka tutarlılığı: [fingofy-legal-pages](https://umaycorporation.github.io/fingofy-legal-pages/) ↔ Play listesi

---

## 5. Sosyal medya (organik)

Hesapları **siz** açarsınız; içerik takvimi repoda hazır.

- [ ] Instagram + TikTok (veya X) — kullanıcı adı, Play ikonu, bio + link
- [ ] 4 haftalık içerik: [SOCIAL_CONTENT_CALENDAR.md](./SOCIAL_CONTENT_CALENDAR.md)
- [ ] Haftada 2–3 gönderi; spam yorum **yapmayın**
- [ ] WhatsApp / aile grubunda **bir kez** samimi öneri

---

## 6. Topluluk ve ağızdan ağıza

- [ ] Finans grupları / forum: önce yardım, link sorulursa veya bio’da
- [ ] Uygulama içi **Paylaş** (Profil veya Dashboard banner)
- [ ] Rehber: [COMMUNITY_ORGANIC_GUIDE.md](./COMMUNITY_ORGANIC_GUIDE.md)

---

## 7. Haftalık ölçüm (her Pazartesi, 15–20 dk)

| Metrik | Nerede | Not |
|--------|--------|-----|
| Kurulumlar | Play Console → User acquisition | Haftalık trend |
| `first_open` | Firebase Analytics → Events | |
| `sign_up` | Firebase Analytics | first_open’a oran |
| `budget_created` | Firebase Analytics | Kayıt sonrası dönüşüm |
| `first_transaction` | Firebase Analytics | |
| `app_share_completed` | Firebase Analytics | Paylaşım banner / menü |
| Çökme / ANR | Android vitals | Artış → hotfix |
| Ortalama puan | Play → Ratings and reviews | 5+ yorum sonrası anlamlı |

### Firebase huni (Console’da bir kez)

1. Analytics → **Explore** → Funnel exploration
2. Adımlar: `first_open` → `sign_up` → `budget_created` → `first_transaction`
3. Haftalık kaydet; düşüş varsa önce ürün/bug, sonra daha fazla paylaşım

Tam rutin: [GROWTH_PLAYBOOK.md](./GROWTH_PLAYBOOK.md) Bölüm 2.

---

## 45 günlük zaman çizelgesi

| Dönem | Odak |
|-------|------|
| Gün 1–3 | Production doğrulama + ASO + test kanalı |
| Gün 4–14 | Screenshot, yorum, sosyal hesap |
| Gün 15–30 | Haftada 2–3 sosyal + 1 topluluk katkısı |
| Gün 31–45 | İçerik devam + funnel + yorum yanıtı |

**Ücretli reklam (UAC, Meta)** bu checklist’in dışında; organik 2–4 hafta veri sonrası [GROWTH_PLAYBOOK.md](./GROWTH_PLAYBOOK.md) Faz 3.
