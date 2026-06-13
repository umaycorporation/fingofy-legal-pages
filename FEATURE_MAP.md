# Fingofy — Modül Haritası

Agent'ların en çok kullanacağı dosya. Yeni özellik veya hata ayıklamada ilgili satırdan başlayın.

## Özet tablo

| Modül | Ekranlar | Context | Service(ler) |
|-------|----------|---------|--------------|
| Auth | `AuthFlow` (navigator) | `AuthContext` | `userService`, Firebase Auth |
| İşlemler | `screens/transactions/*` | `TransactionContext` | `transactionService`, `transactionInstallmentService` |
| Tekrarlayan işlem | `RecurringTransactionsScreen`, `CreateRecurringScreen`, `EditRecurringScreen` | `TransactionContext` | `transactionService` (`recurringTransactions`) |
| Bütçe | `screens/budget/*` | `BudgetContext` | `budgetService` |
| Hedef | `screens/goals/*` | `GoalContext` | `goalService` |
| Borç | `screens/debts/*` | `DebtContext` | `debtService` |
| Hesap | `screens/accounts/*` | `AccountContext` | `accountService`, `creditCardPaymentService` |
| Kasa (Vault) | `screens/vault/*` | `VaultContext` | `vaultService` |
| Plan | `screens/plans/*` | `PlanContext` | `planService` |
| Kategori | `screens/categories/*` | `CategoryContext` | `categoryService` |
| Dashboard | `screens/dashboard/DashboardScreen` | Birden fazla context | `analyticsService` |
| Analytics | `screens/analytics/AnalyticsScreen` | `AnalyticsContext` | `analyticsService`, `analyticsEventService` |
| Bildirim | Profil → Notifications | `NotificationContext` | `notificationService`, `fcmService` |
| Offline | (global) | `OfflineContext` | `offlineStorageService` |
| IAP / Reklam | Profil, banner'lar | `EntitlementsContext` | `purchaseService`, `adService` |
| Growth | `ShareAppBanner` vb. | — | `growthFunnelService`, `storeEngagementService` |
| Dil / Tema | Profil ayarları | `LanguageContext`, `ThemeContext` | — |
| Para birimi | `CurrencySelector` | `CurrencyContext`, `TransactionCurrencyConversionContext` | — |
| Güvenlik | `Security` ekranı | `AuthContext` | `PlayIntegrityService` |
| Yasal | `screens/legal/*` | — | — |

---

## Modül detayları

### İşlemler

| Ekran | Dosya | Route |
|-------|-------|-------|
| Ekle | `AddTransactionScreen.tsx` | `AddTransaction` |
| Liste | `TransactionListScreen.tsx`, `EnhancedTransactionListScreen.tsx` | `TransactionList`, `EnhancedTransactionList` |
| Detay | `TransactionDetailScreen.tsx` | `TransactionDetail` |
| Düzenle | `EditTransactionScreen.tsx` | `EditTransaction` |

**Akış:** Screen → `useTransaction()` → `transactionService` → `transactions` koleksiyonu

### Tekrarlayan işlem

| Ekran | Dosya | Route |
|-------|-------|-------|
| Liste | `RecurringTransactionsScreen.tsx` | `RecurringTransactions` |
| Oluştur | `CreateRecurringScreen.tsx` | `CreateRecurring` |
| Düzenle | `EditRecurringScreen.tsx` | `EditRecurring` |

**Akış:** Screen → `TransactionContext` → `transactionService.createRecurringTransaction()` → `recurringTransactions`

**İlgili fonksiyonlar:** `getRecurringTransactions`, `updateRecurringTransaction`, `deleteRecurringTransaction`, `calculateNextRecurringDate`

### Bütçe

| Ekran | Route |
|-------|-------|
| `BudgetOverviewScreen` | `BudgetOverview` |
| `CreateBudgetScreen` | `CreateBudget` |
| `EditBudgetScreen` | `EditBudget` |
| `BudgetAnalyticsScreen` | `BudgetAnalytics` |

Koleksiyonlar: `budgets`, `budgetTemplates`

### Hedef

| Ekran | Route |
|-------|-------|
| `GoalsListScreen` | `GoalsList` |
| `CreateGoalScreen` | `CreateGoal` |
| `GoalDetailScreen` | `GoalDetail` |
| `EditGoalScreen` | `EditGoal` |

Koleksiyonlar: `financialGoals`, `goalContributions`, `goalMilestones`

### Borç

| Ekran | Route |
|-------|-------|
| `DebtsListScreen` | `DebtsList` |
| `DebtDetailScreen` | `DebtDetail` |
| `CreateDebtScreen` | `CreateDebt` |
| `EditDebtScreen` | `EditDebt` |

Koleksiyonlar: `debts`, `debtPayments`

### Hesap

| Ekran | Route |
|-------|-------|
| `AccountsListScreen` | `AccountsList` |
| `CreateAccountScreen` | `CreateAccount` |
| `AccountDetailScreen` | `AccountDetail` |
| `EditAccountScreen` | `EditAccount` |
| `CreditCardPaymentScreen` | `CreditCardPayment` |

Koleksiyonlar: `userAccounts`, `creditCardPayments`

### Kasa (Vault)

| Ekran | Route |
|-------|-------|
| `VaultDashboardScreen` | `VaultDashboard` |
| `VaultAddScreen` | `VaultAddHolding` |
| `VaultEditScreen` | `VaultEditHolding` |
| `VaultDetailScreen` | `VaultDetailHolding` |

Koleksiyon: `vault_holdings`

**Not:** Vault kasa modülü olarak hesap bakiyesi ve işlemlerden izole tutulur; net worth'a dahil değildir.

### Plan

| Ekran | Route |
|-------|-------|
| `PlansListScreen` | `PlansList` |
| `CreatePlanScreen` | `CreatePlan` |
| `PlanDetailScreen` | `PlanDetail` |

Koleksiyon: `plans`

### Kategori

| Ekran | Route |
|-------|-------|
| `CategoryManagementScreen` | `CategoryManagement` |
| `CreateCategoryScreen` | `CreateCategory` |
| `EditCategoryScreen` | `EditCategory` |

Koleksiyonlar: `categories` (kullanıcı), `defaultCategories` (global)

### Bildirim

- `NotificationContext` — liste yükleme, oluşturma
- `notificationService` — `notifications` koleksiyonu
- `fcmService` — `fcmQueue`, `deviceTokens`
- Profil: `Notifications`, `NotificationList` ekranları

### Growth / Analytics

| Service | Kullanım |
|---------|----------|
| `growthFunnelService` | Paylaşım banner, funnel event'leri |
| `analyticsEventService` | Genel Firebase Analytics |
| `analyticsService` | İşlem bazlı analitik sorguları |
| `storeEngagementService` | Mağaza değerlendirme, paylaşım |

Bileşen: `components/growth/ShareAppBanner.tsx`

---

## Yeni özellik checklist

1. `docs/FEATURE_MAP.md` — bu tabloya satır ekle
2. `screens/<modül>/` — ekran dosyası
3. `navigation/AppNavigator.tsx` — route + `RootStackParamList`
4. `contexts/` — state metodu (gerekirse)
5. `services/` — Firestore CRUD
6. `constants/translations/*.ts` — tüm diller
7. `firestore.rules` — yeni koleksiyon ise kural ekle
8. `npm run quality` + `npm run i18n:check`
