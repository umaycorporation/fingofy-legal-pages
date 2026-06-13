# Fingofy — Mimari

## Genel bakış

```
UI (screens/) → Context (contexts/) → Service (services/) → Firestore / Functions
                      ↓
              OfflineContext + offlineStorageService (yerel önbellek)
```

## Navigasyon

Ana yapı: **Root Stack** → `MainTabs` (bottom tab) + modal/stack ekranlar.

### Tab'lar (`TabParamList`)

| Tab | Ekran | Açıklama |
|-----|-------|----------|
| `Dashboard` | `DashboardScreen` | Ana özet |
| `Applications` | `ApplicationsOverviewScreen` | Modül girişleri |
| `Profile` | Profil menüsü | Ayarlar, dil, tema |

### Root Stack ekranları (`RootStackParamList`)

Tüm stack ekranları `navigation/AppNavigator.tsx` içinde tanımlı. Gruplar:

- **Auth:** `Auth` (giriş akışı)
- **İşlemler:** `AddTransaction`, `TransactionList`, `EnhancedTransactionList`, `EditTransaction`, `TransactionDetail`, `RecurringTransactions`, `CreateRecurring`, `EditRecurring`
- **Bütçe:** `BudgetOverview`, `CreateBudget`, `EditBudget`, `BudgetAnalytics`
- **Hedef:** `GoalsList`, `CreateGoal`, `GoalDetail`, `EditGoal`
- **Borç:** `DebtsList`, `DebtDetail`, `CreateDebt`, `EditDebt`
- **Plan:** `PlansList`, `CreatePlan`, `PlanDetail`, `CompletedPlans`
- **Hesap:** `AccountsList`, `CreateAccount`, `AccountDetail`, `EditAccount`, `CreditCardPayment`
- **Kasa:** `VaultDashboard`, `VaultAddHolding`, `VaultEditHolding`, `VaultDetailHolding`
- **Kategori:** `CategoryManagement`, `CreateCategory`, `EditCategory`
- **Profil:** `ProfileMenu`, `EditProfile`, `LanguageSelector`, `Security`, `Notifications`, `NotificationList`, `HelpSupport`, `CurrencySelector`, `ThemeSelector`
- **Analytics:** `Analytics`

Yeni ekran: `RootStackParamList` tipine ekle → `Stack.Screen` kaydet → `screens/<modül>/` dosyası oluştur.

## State — Context listesi (20)

| Context | Sorumluluk |
|---------|------------|
| `AuthContext` | Firebase Auth, oturum |
| `LanguageContext` | Dil seçimi, `texts` |
| `ThemeContext` | Açık/koyu tema |
| `NotificationContext` | Bildirim listesi, ayarlar |
| `ToastContext` | Toast mesajları |
| `EntitlementsContext` | IAP durumu (adsRemoved, premium) |
| `AlertContext` | Onay/uyarı dialogları |
| `AnalyticsContext` | Uygulama içi analitik state |
| `OfflineContext` | Ağ durumu, kuyruk |
| `PlanContext` | Finansal planlar |
| `CurrencyContext` | Para birimi |
| `AccountContext` | Banka/nakit/kredi kartı hesapları |
| `CategoryContext` | İşlem kategorileri |
| `BudgetContext` | Bütçeler ve şablonlar |
| `TransactionContext` | İşlemler + tekrarlayan işlemler |
| `VaultContext` | Kasa varlıkları (altın, döviz, kripto vb.) |
| `TransactionCurrencyConversionContext` | Çoklu para birimi dönüşümü |
| `GoalContext` | Finansal hedefler |
| `DebtContext` | Borçlar ve ödemeler |

## Veri akışı örnekleri

### İşlem ekleme

1. `AddTransactionScreen` form submit
2. `TransactionContext.addTransaction()`
3. `transactionService.createTransaction()` → Firestore `transactions`
4. Offline ise `OfflineContext` kuyruğa alır
5. `AccountContext` bakiye güncellemesi (ilgili hesap varsa)

### Tekrarlayan işlem

1. `CreateRecurringScreen` → `TransactionContext`
2. `transactionService.createRecurringTransaction()` → `recurringTransactions`
3. Cloud Function veya client tarafı zamanlama ile `transactions` oluşturur

## Offline / sync

- `OfflineContext`: NetInfo ile bağlantı izleme
- `offlineStorageService`: AsyncStorage tabanlı kuyruk — **yalnızca plan modülü** için aktif
- Plan offline create: yerel `savePlans` + sync kuyruğu; online olunca `planService.applySyncQueueItem`
- İşlem/bütçe modülleri şu an offline kuyrukta değil

## Vault (Kasa)

Vault (`vault_holdings`) kasa modülü olarak **bilinçli olarak izole** tutulur: hesap bakiyesi ve işlem geçmişine otomatik bağlanmaz. Net worth hesaplamasına dahil edilmez. Gelecekte entegrasyon ürün kararı ile yapılabilir.

## Monetizasyon

| Bileşen | Dosya |
|---------|-------|
| Reklamlar | `services/adService.ts` |
| IAP satın alma | `services/purchaseService.ts` |
| Entitlement state | `contexts/EntitlementsContext.tsx` |
| Sunucu doğrulama | `functions/` → `verifyPlayPurchase` |
| Growth funnel | `services/growthFunnelService.ts` |

`users.entitlements` alanı yalnızca Cloud Functions (Admin SDK) tarafından yazılabilir.

## Güvenlik

- **App Check:** Play Integrity (production'da aktif)
- **Firestore Rules:** `firestore.rules` — owner `userId` kontrolü
- **Play Integrity:** `PlayIntegrityService` — `App.tsx` başlangıç kontrolü
- **Detay:** [SECURITY_PLAYBOOK.md](SECURITY_PLAYBOOK.md)

## Analytics

- `analyticsEventService`: Firebase Analytics event'leri
- `growthFunnelService`: Büyüme hunisi event'leri (`share_banner_shown`, vb.)
- `loggingService`: `appLogs` koleksiyonu

## Cloud Functions (`functions/`)

Önemli fonksiyonlar:

- `verifyPlayPurchase` — IAP doğrulama, entitlement yazma
- `logSecurityEvent` — güvenlik logları
- `verifyPlayIntegrityToken` — integrity doğrulama
- FCM kuyruk işleme (`fcmQueue`)

Deploy: `npm run deploy:security` veya `npm run deploy:functions`

## Kararlar (mimari notlar)

| Tarih | Karar | Gerekçe |
|-------|-------|---------|
| — | Context + Service ayrımı | Firestore erişimi yalnızca service katmanında |
| — | `userId` her owner dokümanda | Firestore rules `isOwner(userId)` pattern |
| — | Entitlements sunucu yazımı | İstemci manipülasyonunu önler |
| — | Metro Windows patch | `scripts/patch-metro-watcher-timeout.js` — emülatör 500 hatası |
| 2026-05 | Plan ödeme tek kaynak | `paymentPlan[].isCompleted`; `paidPayments` türetilir |
| 2026-05 | Offline sync kapsamı | Yalnızca plan modülü; transaction offline yok |
| 2026-05 | Vault izolasyonu | Net worth ve hesaplara dahil değil (ayrı modül) |

Yeni mimari kararlar bu tabloya eklenmeli.
