# Play Store — Yorum Console’da var, mağaza sayfasında yok

Bu rehber, **Play Console → Ratings and reviews (Production)** altında yorum görünürken, [mağaza listesinde](https://play.google.com/store/apps/details?id=com.umaycorporation.fingofy) puan/yorum çıkmaması durumunda adım adım teşhis içindir.

Fingofy uygulama kodu mağaza yorumlarını gizlemez; sorun Google Play tarafındaki kaynak, moderasyon veya görüntüleme koşullarından kaynaklanır.

---

## Hızlı özet: en sık 3 neden

1. **Moderasyon gecikmesi** — Console’da görünür, mağazada 24–72 saat sonra yansır.
2. **Geliştirici / lisans test hesabı** — Console’da listelenir, herkese açık mağazada filtrelenir.
3. **Play dışı kurulum** — sideload, `gradlew install`, internal/open test APK ile bırakılan değerlendirme mağazada sayılmaz.

---

## Adım 1 — Console’da yorum durumunu doğrula

**Konum:** Play Console → **Grow → Store presence → Ratings and reviews** → filtre **Production**

Her yorum için aşağıdaki tabloyu doldurun:

| Yorum # | Durum (Published/Pending) | Public review / Private feedback | Play’den kurulum? | Geliştirici veya test hesabı? | Yazılı yorum var mı? |
|---------|---------------------------|----------------------------------|-------------------|-------------------------------|----------------------|
| 1 | | | | | |
| 2 | | | | | |

### Ne arıyorsunuz?

| Kontrol | Beklenen (mağazada görünmesi için) |
|---------|-------------------------------------|
| **Durum** | `Published` (Pending ise bekleyin) |
| **Tür** | **Public review** (private feedback mağazada görünmez) |
| **Kaynak** | Google Play üzerinden; test rozeti olmamalı |
| **Hesap** | Play Console erişimi veya license testing listesinde **olmamalı** |
| **Kurulum** | Kullanıcı uygulamayı **Play Store production** sürümünden kurmuş olmalı |

**Sayılmayan kurulumlar:** sideload APK, `gradlew` ile doğrudan yükleme, internal/open/closed test kanalı.

---

## Adım 2 — Mağaza sayfasını doğru kontrol et

### 2a. Tarayıcı (tercih edilen)

1. Gizli/özel pencere açın (**geliştirici hesabıyla giriş yapmayın**).
2. Açın: https://play.google.com/store/apps/details?id=com.umaycorporation.fingofy
3. Sayfayı **aşağı kaydırın** → **Değerlendirmeler ve yorumlar** bölümü.
4. Paket adının `com.umaycorporation.fingofy` olduğunu doğrulayın.

### 2b. Play Store uygulaması (telefon)

- [ ] Geliştirici Google hesabı **çıkış yapılmış** veya bağımsız hesap kullanılıyor
- [ ] Play Store → Profil → Uygulamalar ve cihaz yönetimi → **Play Store** → **Önbelleği temizle**
- [ ] Uygulama sayfasını yeniden açın; Değerlendirmeler bölümüne inin

### Mağazada olası görünümler

| Görünüm | Yorum |
|---------|--------|
| Hiç puan / “Henüz değerlendirme yok” | Public listeye henüz yansımamış veya geçerli production yorumu yok |
| Puan var, yazılı yorum az/yok | Sadece yıldız verilmiş olabilir |
| Puan + yorumlar | Sorun geçiciydi (önbellek veya gecikme) |

---

## Adım 3 — Yayın ve dağıtım doğrulama

**Release → Production**

- [ ] Rollout **%100** (kademeli yayın tamamlandı mı?)
- [ ] Sürüm **Active** / kullanıcılar tarafından indirilebilir

**Dashboard → Countries/regions**

- [ ] **Türkiye** dağıtım listesinde ve kapalı değil
- [ ] Uygulama durumu: **Available on Google Play**

**Setup → License testing** (varsa)

- [ ] Yorum bırakan e-posta bu listede **değil** (test hesabı yorumları mağazada filtrelenir)

---

## Adım 4 — Geçerli production yorumu nasıl toplanır?

Uygulama içi (v1.1.17+):

- **Profil → Uygulamayı değerlendir** — Play In-App Review API
- **Profil → Uygulamayı paylaş** — mağaza linki

Koşullar:

1. Kullanıcı uygulamayı **Play’den** indirmiş olmalı.
2. Hesap geliştirici / license test **olmamalı**.
3. İlk otomatik istem: kurulumdan **3+ gün** sonra ve en az bir bütçe veya işlem sonrası (90 gün cooldown).

Teknik referans: [storeEngagementService.ts](../services/storeEngagementService.ts), [storeLinks.ts](../constants/storeLinks.ts).

---

## Adım 5 — Zaman çizelgesi

| Süre | Aksiyon |
|------|---------|
| 0–24 saat | Console’da Published mı kontrol; mağazayı gizli sekmede kontrol |
| 24–48 saat | Play Store önbelleği temizle; bağımsız hesapla tekrar bak |
| 48–72 saat | Hâlâ yoksa Adım 1 tablosunu gözden geçir (test hesabı / private feedback) |
| 72+ saat | Aşağıdaki destek adımına geç |

---

## Adım 6 — Google Play desteğine ne zaman yazılır?

**Tümü doğruysa** ve 72 saat sonra mağazada hâlâ **hiç puan** görünmüyorsa:

1. Play Console → **Help** → **Contact support**
2. Konu: *Ratings and reviews not visible on public store listing*
3. Ekleyin:
   - Paket: `com.umaycorporation.fingofy`
   - Mağaza URL
   - Console’da Production yorum sayısı ve Published durumu
   - Bağımsız hesap + Play kurulumu ile test edildiği
   - Rollout %100, Türkiye açık

---

## İlgili dokümanlar

- [GROWTH_PLAYBOOK.md](./GROWTH_PLAYBOOK.md) — büyüme ve ASO
- [PLAY_CONSOLE_ANDROID_COMPLIANCE.md](./PLAY_CONSOLE_ANDROID_COMPLIANCE.md) — teknik uyumluluk
