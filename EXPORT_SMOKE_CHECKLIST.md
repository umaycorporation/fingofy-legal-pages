# Export Smoke Checklist (CSV / PDF / Word)

Bu checklist, export geliştirmeleri sonrası manuel doğrulama için hazırlanmıştır.

## 1) Android Smoke (zorunlu)

- `EnhancedTransactionListScreen` aç.
- Export ikonu -> format menüsü açılmalı.
- Sırayla `CSV`, `PDF`, `Word` seç ve her biri için:
  - Loading görünmeli.
  - Format satırları ve menü kapatma butonu işlem süresince pasif olmalı.
  - Aynı anda ikinci export tetiklenememeli.
  - Paylaşım ekranı açılmalı.
- Paylaşım tamamlanınca başarı toast görünmeli.
- Paylaşım iptal edilirse başarı toast görünmemeli.

## 2) Dosya İçeriği Doğrulama

- CSV:
  - Başlık satırı mevcut olmalı.
  - Özel karakterler (virgül, tırnak, satır sonu) bozulmamalı.
- PDF:
  - Türkçe karakterler (`ğüşiöçİ`) okunabilir olmalı.
  - En az 20+ kayıtla sayfa kırılımı bozulmamalı.
- Word (`.docx`):
  - Başlık + tablo yapısı korunmalı.
  - Açıklama ve etiket alanları doğru görünmeli.

## 3) Hedef Uygulama Paylaşım Kontrolü

Her format için en az bir kez:

- Google Drive / Files
- WhatsApp veya Telegram
- E-posta istemcisi

Not: Android üretim cihazlarında bazı hedefler MIME tipine göre filtrelenebilir.

## 4) iOS Doğrulama (macOS üzerinde)

- `cd ios && pod install`
- Debug build al (`react-native run-ios` veya Xcode).
- Android ile aynı export akışlarını tekrar et.
- iOS share sheet üzerinde:
  - Dosya adı doğru mu?
  - CSV/PDF/DOCX hedef uygulamalarına yönleniyor mu?

## 5) Regresyon Hızlı Kontrol

- Transaction listesi açılışı ve filtreleme etkilenmemeli.
- Export menüsü kapat/aç davranışı stabil olmalı.
- Uygulama arka plana alınıp geri dönünce export menüsü takılı kalmamalı.

## 6) Bilinen Durumlar

- PDF Unicode fontu ilk kullanımda ağdan alınır, sonrasında cache kullanılır.
- Tam `test:unit` koşusunda export dışı mevcut test kırıkları olabilir; export için kritik olan testler:
  - `__tests__/csv.test.ts`
  - `__tests__/transactionExport.test.ts`
