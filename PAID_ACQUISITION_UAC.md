# Ücretli Edinim — Google UAC (Hafta 8+)

**Ön koşul:** 4–6 hafta organik veri + 10+ gerçek Play yorumu + stabil huni.

Organik plan: [GROWTH_PLAYBOOK.md](./GROWTH_PLAYBOOK.md) | [FREE_USER_ACQUISITION_CHECKLIST.md](./FREE_USER_ACQUISITION_CHECKLIST.md)

**Play linki:** https://play.google.com/store/apps/details?id=com.umaycorporation.fingofy

---

## Ne zaman başlamalı?

| Kriter | Eşik |
|--------|------|
| Organik haftalar | ≥ 4 |
| Gerçek Play yorumu | ≥ 10 |
| Ortalama puan | ≥ 4.0 |
| `sign_up` / `first_open` | ≥ %20 ve stabil |
| ASO EN + TR | Güncel |

Hepsi yeşil değilse organik kanallara devam edin.

---

## Hazırlık kontrol listesi

### Mağaza

- [ ] [GLOBAL_ASO_CHECKLIST.md](../store-assets/metadata/GLOBAL_ASO_CHECKLIST.md) tamamlandı
- [ ] 6–8 screenshot + feature graphic
- [ ] EN ve TR custom listings

### Kreatif

- [ ] 15–30 sn dikey video (TikTok/Reels’ten) — EN ve TR versiyon
- [ ] 3–5 statik görsel (screenshot veya carousel export)
- [ ] Play listing URL kampanyada kullanılacak

### Ölçüm

- [ ] Firebase `first_open` ve `sign_up` olayları geliyor
- [ ] Play Console → **User acquisition** referrer takibi açık
- [ ] Haftalık baseline kurulum notu var → [WEEKLY_FUNNEL_REPORT.md](./WEEKLY_FUNNEL_REPORT.md)

### Monetizasyon karşılaştırması

- [ ] AdMob haftalık gelir (varsa) not edildi
- [ ] Hedef CPI < LTV tahmini — [MONETIZATION_PLAYBOOK.md](./MONETIZATION_PLAYBOOK.md)

---

## Google App Campaigns (UAC) — test kampanyası

### Başlangıç ayarları

| Alan | Öneri |
|------|-------|
| Kampanya türü | App installs |
| Platform | Android |
| Bütçe | Günlük **$5–15** (test) |
| Süre | 14 gün test |
| Hedef | Play install |
| Konum | US, UK, TR, DE (veriye göre) |
| Dil | English + Turkish kreatif |

### Kurulum adımları

1. [Google Ads](https://ads.google.com/) → **New campaign** → **App promotion**
2. Uygulama: `com.umaycorporation.fingofy` (Play Console bağlantısı)
3. Video + görsel + metin yükleyin (EN öncelik)
4. Günlük bütçe cap — ilk hafta düşük tutun
5. Dönüşüm: Play install (Google otomatik optimize eder)

### İzleme (haftalık)

| Metrik | Kaynak | Not |
|--------|--------|-----|
| CPI (maliyet / kurulum) | Google Ads | Organik baseline ile karşılaştır |
| Kurulum artışı | Play Console | UAC başlangıç tarihi işaretle |
| `first_open` artışı | Firebase | Gecikme olabilir |
| Kalite | `budget_created` oranı | Düşükse kreatif değiştir |

---

## Meta (Facebook / Instagram) — ikinci dalga

UAC 2 hafta veri verdikten sonra:

- Aynı dikey videoyu Reels ads formatında
- Günlük $5 test
- Retargeting: 1000+ kurulum sonrası düşük öncelik

---

## Yapılmayacaklar (ilk testte)

- Günlük $50+ bütçe
- Influencer ödemesi (organik veri yokken)
- Aynı anda UAC + Meta + TikTok ads
- Mağaza listesi optimize edilmeden paid

---

## 14 günlük test sonrası karar

| Sonuç | Aksiyon |
|-------|---------|
| CPI kabul edilebilir + `budget_created` iyi | Bütçeyi %20 artır, 2 hafta daha |
| CPI yüksek, düşük dönüşüm | Kreatif / ASO revizyon, paid duraklat |
| Kurulum var, huni zayıf | Ürün UX önce; paid ikinci plan |

---

## Kayıt şablonu

```
UAC başlangıç: ____-__-__
Günlük bütçe: $____
14g toplam harcama: $____
14g Play kurulum (UAC dönemi): ____
Tahmini CPI: $____
budget_created / sign_up (UAC sonrası kohort): ____%
Karar: devam / duraklat / ölçekle
```
