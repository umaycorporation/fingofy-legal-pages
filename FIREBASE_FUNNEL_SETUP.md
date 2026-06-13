# Firebase Analytics — Growth Huni Kurulumu

Bu rehber [GROWTH_PLAYBOOK.md](./GROWTH_PLAYBOOK.md) Faz 0 adımıdır. Kod tarafı hazır; Firebase Console’da bir kez yapılandırın.

## Uygulama tarafı (repoda mevcut)

| Olay | Tetikleyici | Dosya |
|------|-------------|-------|
| `first_open` | İlk uygulama açılışı | [growthFunnelService.ts](../services/growthFunnelService.ts) |
| `sign_up` | E-posta veya Google kayıt | [AuthContext.tsx](../contexts/AuthContext.tsx) |
| `budget_created` | İlk / yeni bütçe | [BudgetContext.tsx](../contexts/BudgetContext.tsx) |
| `first_transaction` | İlk işlem | [TransactionContext.tsx](../contexts/TransactionContext.tsx) |
| `app_share_completed` | Paylaşım tamamlandı | [storeEngagementService.ts](../services/storeEngagementService.ts) |
| `share_banner_shown` | Dashboard paylaşım banner göründü | [ShareAppBanner.tsx](../components/growth/ShareAppBanner.tsx) |
| `share_banner_dismissed` | Banner kapatıldı | Aynı |
| `store_review_flow_shown` | In-app review akışı | [storeEngagementService.ts](../services/storeEngagementService.ts) |

Yerel doğrulama: `npm run verify:growth-build`

---

## Firebase Console — Funnel exploration (bir kez)

1. [Firebase Console](https://console.firebase.google.com/) → projeniz → **Analytics**
2. Sol menü → **Explore** → **Funnel exploration** → **Create new**
3. Ad: `Growth funnel — core`
4. Adımlar (sırayla ekleyin):

   | Sıra | Event name |
   |------|------------|
   | 1 | `first_open` |
   | 2 | `sign_up` |
   | 3 | `budget_created` |
   | 4 | `first_transaction` |

5. **Save** → haftalık kontrol için yer imi ekleyin

### İkinci huni (paylaşım)

| Sıra | Event name |
|------|------------|
| 1 | `budget_created` |
| 2 | `share_banner_shown` |
| 3 | `app_share_completed` |

---

## Önerilen eşikler

| Adım | Hedef | Düşükse aksiyon |
|------|-------|-----------------|
| `sign_up` / `first_open` | ≥ %25 | Onboarding / kayıt UX |
| `budget_created` / `sign_up` | ≥ %40 | İlk bütçe rehberi |
| `first_transaction` / `budget_created` | ≥ %50 | İşlem ekleme CTA |
| `app_share_completed` | Artış trendi | Sosyal + banner |

Haftalık rapor şablonu: [WEEKLY_FUNNEL_REPORT.md](./WEEKLY_FUNNEL_REPORT.md)

---

## Play Console ile birlikte okuma

| Kaynak | Ne ölçer |
|--------|----------|
| Play Console → User acquisition | Mağaza kurulumları |
| Firebase `first_open` | Uygulama açılışı (cihaz) |
| Firebase `sign_up` | Hesap oluşturma |

Kurulum > `first_open` farkı normaldir (indirme ≠ açılış).

---

## Debug (geliştirici)

- Firebase Console → **DebugView** — test cihazında `adb` ile debug mode
- Production’da olaylar 24 saate kadar gecikebilir; haftalık rapor için yeterli
