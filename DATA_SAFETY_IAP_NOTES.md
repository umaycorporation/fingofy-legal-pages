# Data Safety — IAP / Satın Alma Notları (Play Console)

Play Console → **App content → Data safety** formunda güncelleme rehberi.

## Satın alma verisi

| Soru | Önerilen cevap |
|------|----------------|
| Uygulama kullanıcıların satın alma geçmişini topluyor mu? | **Evet** — Google Play üzerinden |
| Veri türü | Purchase history |
| Amaç | Uygulama işlevselliği (reklam kaldırma hakkı) |
| Paylaşım | Google Play Billing; Firebase’de yalnızca `entitlements.adsRemoved` bayrağı |
| Şifreleme | Aktarımda şifreli (HTTPS / Firebase) |

## Gizlilik politikası metni (eklenecek paragraf)

Türkçe özet (legal-pages sitesine ekleyin):

```
Uygulama İçi Satın Almalar: Fingofy, Google Play Billing üzerinden isteğe bağlı 
satın alma sunar (ör. reklamları kaldırma). Ödeme Google tarafından işlenir; 
kart bilgileriniz uygulamamızda saklanmaz. Satın alma durumunuz, reklamsız 
deneyimi etkinleştirmek için hesabınızla ilişkili olarak güvenli sunucularımızda 
(adsRemoved bayrağı) tutululabilir. İade ve iptal koşulları Google Play politikalarına tabidir.
```

## Mağaza listesi

[play-store-description.txt](../store-assets/metadata/play-store-description.txt) — reklamsız sürüm IAP ile belirtildi.
