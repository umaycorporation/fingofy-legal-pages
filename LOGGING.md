# Log Mekanizması Dokümantasyonu

## Genel Bakış

Uygulama, tüm hataları ve önemli olayları Firebase Firestore'a kaydeden merkezi bir log mekanizmasına sahiptir.

**ÖNEMLİ**: Kullanıcılar log'ları göremez. Sadece developer/admin Firebase Console'dan log'ları görüntüleyebilir.

## Log Yapısı

### Firebase Collection: `appLogs`

Tüm log'lar `appLogs` collection'ında saklanır. Her log şu bilgileri içerir:

- `level`: Log seviyesi (DEBUG, INFO, WARN, ERROR, CRITICAL)
- `message`: Log mesajı
- `context`: Log'un geldiği context/component
- `userId`: Kullanıcı ID (varsa)
- `error`: Hata detayları (varsa)
- `metadata`: Ek bilgiler
- `timestamp`: Log zamanı
- `deviceInfo`: Cihaz bilgileri

## Log Seviyeleri

1. **DEBUG**: Geliştirme için detaylı bilgiler
2. **INFO**: Genel bilgilendirme mesajları
3. **WARN**: Uyarı mesajları
4. **ERROR**: Hata mesajları
5. **CRITICAL**: Kritik hatalar (uygulama çökmesi gibi)

## Log'ları Görüntüleme

### Firebase Console'dan (Sadece Developer/Admin)

1. Firebase Console'a gidin: https://console.firebase.google.com
2. Projenizi seçin
3. Sol menüden **Firestore Database**'e tıklayın
4. `appLogs` collection'ını açın
5. Log'ları filtreleyebilirsiniz:
   - `level` = "ERROR" → Sadece hatalar
   - `level` = "CRITICAL" → Kritik hatalar
   - `context` = "TransactionContext" → Belirli context
   - `userId` = "USER_ID" → Belirli kullanıcı
   - `timestamp` → Tarihe göre

**Not**: Firestore rules nedeniyle kullanıcılar log'ları göremez. Sadece Firebase admin panelinden görüntülenebilir.

### Firestore Query Örnekleri

```javascript
// Son 100 ERROR log'unu getir
const errorLogs = await getDocs(
  query(
    collection(db, 'appLogs'),
    where('level', '==', 'ERROR'),
    orderBy('timestamp', 'desc'),
    limit(100)
  )
);

// Belirli bir kullanıcının log'larını getir
const userLogs = await getDocs(
  query(
    collection(db, 'appLogs'),
    where('userId', '==', 'USER_ID'),
    orderBy('timestamp', 'desc'),
    limit(100)
  )
);

// Bugünkü CRITICAL log'ları getir
const today = new Date();
today.setHours(0, 0, 0, 0);
const criticalLogs = await getDocs(
  query(
    collection(db, 'appLogs'),
    where('level', '==', 'CRITICAL'),
    where('timestamp', '>=', Timestamp.fromDate(today)),
    orderBy('timestamp', 'desc')
  )
);
```

## Kod İçinde Log Kullanımı

### Temel Kullanım

```typescript
import { loggingService } from '../services/loggingService';

// Info log
loggingService.info('İşlem başarılı', 'TransactionContext');

// Error log
try {
  // some code
} catch (error) {
  loggingService.error(
    'Transaction oluşturma hatası',
    error,
    'TransactionContext',
    { transactionId: '123' }
  );
}

// Critical log
loggingService.critical(
  'Uygulama çökmesi',
  error,
  'ErrorBoundary',
  { componentStack: errorInfo.componentStack }
);
```

### Mevcut Console.log'ları Entegre Etme

Mevcut `console.log` çağrılarınız otomatik olarak Firebase'e gönderilmez. Önemli log'lar için `loggingService` kullanın:

```typescript
// Önceki (sadece console)
console.log('📊 TransactionContext: Transaction yüklendi');

// Yeni (console + Firebase)
loggingService.info('Transaction yüklendi', 'TransactionContext', {
  transactionCount: transactions.length
});
```

## Log Batch Mekanizması

Log'lar performans için batch olarak gönderilir:
- Her 5 saniyede bir veya 50 log biriktiğinde
- Uygulama kapanırken kalan log'lar gönderilir

## Güvenlik

- Log'lar sadece authenticated kullanıcılar tarafından yazılabilir
- Kullanıcılar sadece kendi log'larını okuyabilir
- Log'lar silinemez (sadece okuma izni)

## Firestore Indexes

Log'ları sorgularken gerekli index'ler otomatik oluşturulur. Eğer index hatası alırsanız, Firebase Console'dan index oluşturma linkini takip edin.

## Öneriler

1. **Production'da DEBUG log'larını azaltın**: Sadece önemli bilgileri log'layın
2. **Metadata kullanın**: Hata ayıklamayı kolaylaştırmak için ek bilgiler ekleyin
3. **Context belirtin**: Her log'da context belirtin (hangi component/service)
4. **Kritik hataları önceliklendirin**: CRITICAL log'ları düzenli kontrol edin

## Monitoring

Firebase Console'da:
1. **Firestore Usage** sekmesinden log sayısını kontrol edin
2. **Firestore Rules** sekmesinden güvenlik ayarlarını kontrol edin
3. **Indexes** sekmesinden index durumunu kontrol edin

## Örnek Log Görüntüleme Script'i

```typescript
import { loggingService } from './services/loggingService';

// Son 50 ERROR log'unu getir
const errorLogs = await loggingService.getLogs(
  undefined, // userId (undefined = tüm kullanıcılar)
  'ERROR',   // level
  50         // limit
);

console.log('Error Logs:', errorLogs);
```

## Sorun Giderme

### Log'lar Firebase'e gitmiyor

1. Firebase bağlantısını kontrol edin
2. Firestore rules'u kontrol edin
3. User authentication durumunu kontrol edin
4. Console'da hata mesajlarını kontrol edin

### Çok fazla log birikiyor

1. DEBUG log'larını azaltın
2. Batch size'ı artırın (MAX_BATCH_SIZE)
3. Flush interval'ı artırın (5 saniye → 10 saniye)
