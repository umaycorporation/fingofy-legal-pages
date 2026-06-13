# Fingofy — Veri Modeli (Firestore)

Tüm kullanıcı verisi `userId` (veya `users` için `uid`) ile sahiplik kontrolü altındadır. Detaylı kurallar: `firestore.rules`

## Koleksiyon tablosu

| Koleksiyon | Service / Context | Ana alanlar | Not |
|------------|-------------------|-------------|-----|
| `users` | `userService`, `AuthContext` | `uid`, profil, `entitlements` | `entitlements` yalnızca Functions yazar |
| `transactions` | `transactionService`, `TransactionContext` | `userId`, amount, category, date | Ana işlem kaydı |
| `recurringTransactions` | `transactionService`, `TransactionContext` | `userId`, frequency, nextDate | Tekrarlayan işlem şablonu |
| `transaction_installments` | `transactionInstallmentService` | `userId`, parent transaction | Taksitli işlemler |
| `budgets` | `budgetService`, `BudgetContext` | `userId`, category, limit | |
| `budgetTemplates` | `budgetService`, `BudgetContext` | `userId`, template data | |
| `financialGoals` | `goalService`, `GoalContext` | `userId`, targetAmount | |
| `goalContributions` | `goalService`, `GoalContext` | `userId`, goalId, amount | |
| `goalMilestones` | `goalService`, `GoalContext` | `userId`, goalId | |
| `plans` | `planService`, `PlanContext` | `userId`, title, dates | Finansal planlar |
| `debts` | `debtService`, `DebtContext` | `userId`, totalAmount | |
| `debtPayments` | `debtService`, `DebtContext` | `userId`, debtId | |
| `userAccounts` | `accountService`, `AccountContext` | `userId`, type, balance | Kullanıcı hesapları |
| `accounts` | `accountService` | Global hesap şablonları | |
| `defaultAccounts` | — | Varsayılan hesap tipleri | Okuma herkese |
| `creditCardPayments` | `creditCardPaymentService` | `userId`, cardId | |
| `vault_holdings` | `vaultService`, `VaultContext` | `userId`, type, quantity | Altın, döviz, kripto vb. |
| `categories` | `categoryService`, `CategoryContext` | `userId`, name, icon | Kullanıcı kategorileri |
| `defaultCategories` | `categoryService` | Global varsayılanlar | Okuma herkese |
| `notifications` | `notificationService`, `NotificationContext` | `userId`, type, read | |
| `notificationSettings` | `notificationService` | Bildirim tercihleri | |
| `userNotificationSettings` | `notificationService` | Kullanıcı bazlı ayarlar | |
| `deviceTokens` | `fcmService` | `userId`, token, platform | FCM push |
| `fcmQueue` | `fcmService`, Functions | `userId`, title, body | Push kuyruğu |
| `analytics` | — | — | İstemci okuma kapalı |
| `appLogs` | `loggingService` | level, message, userId? | Alan validasyonu zorunlu |
| `securityLogs` | Functions | — | İstemci erişimi kapalı |
| `purchaseVerifications` | Functions | — | IAP doğrulama kayıtları |
| `_rateLimits` | Functions | — | Rate limiting |

## Sahiplik kuralları (özet)

```javascript
function isOwner(userId) {
  return request.auth != null && request.auth.uid == userId;
}
```

Çoğu koleksiyon:

- **read/create/update/delete:** `isOwner(resource.data.userId)` veya create'te `isOwner(request.resource.data.userId)`
- **userId değiştirilemez:** `userIdUnchanged()` helper

### Özel kurallar

| Koleksiyon | Kısıt |
|------------|-------|
| `users` | `entitlements` istemci yazamaz; `uid` değiştirilemez |
| `defaultCategories`, `defaultAccounts` | Okuma açık, yazma kapalı (veya admin) |
| `analytics` | İstemci okuma/yazma kapalı |
| `securityLogs`, `purchaseVerifications`, `_rateLimits` | İstemci erişimi kapalı |
| `appLogs` | Create: alan validasyonu; userId opsiyonel ama auth uid ile eşleşmeli |
| `fcmQueue` | Create: payload validasyonu; Function işler |
| `vault_holdings` | Doküman ID = holding ID pattern |

## İndeksler

Composite index tanımları: `firestore.indexes.json`

Yeni sorgu eklerken:

1. Service'te `query()` + `where()` + `orderBy()` kullan
2. Emulator veya deploy sonrası index hatası alırsan `firestore.indexes.json` güncelle
3. `npm run deploy:indexes`

## Kullanıcı silme

`userService.deleteUserData()` tüm ilişkili koleksiyonları temizler:

`plans`, `transactions`, `recurringTransactions`, `categories`, `userAccounts`, `accounts`, `budgets`, `budgetTemplates`, `financialGoals`, `goalContributions`, `deviceTokens`, `goalMilestones`

## Test

```bash
npm run test:rules
```

Rules değişikliği sonrası mutlaka çalıştır. Test dosyası: `__tests__/firestore.rules.test.ts`
