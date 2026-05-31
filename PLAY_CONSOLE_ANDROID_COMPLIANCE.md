# Play Console Android Uyumluluk Rehberi

Bu doküman Play Console’da görünen Android 15/16 uyarıları ve AdMob `app-ads.txt` doğrulaması için adım adım kontrol listesidir.

## Özet

| # | Konu | Durum (repo) | Sonraki adım |
|---|------|--------------|--------------|
| 1 | Uçtan uca ekran (SDK 35) | `enableEdgeToEdge()`, opt-out kaldırıldı | Yeni build + cihazda test |
| 2 | Deprecated status/navigation bar API | `statusBarColor` kaldırıldı, JS `backgroundColor` Android’de kapalı | Yeni build |
| 3 | Büyük ekran / yön kısıtı | `screenOrientation` manifest’ten kaldırıldı | Tablet/emülatörde test |
| 4 | 16 KB page size | NDK 28, Fresco 3.6.0, `useLegacyPackaging=false` | `npm run check:android-16kb` + yeni AAB |
| 5 | AdMob app-ads.txt | `docs/app-ads.txt` hazır | Legal-pages repo + Play alanları |
| 6 | Mağaza yorum görünürlüğü | In-app review (v1.1.17+) | [PLAY_STORE_REVIEWS_TROUBLESHOOTING.md](./PLAY_STORE_REVIEWS_TROUBLESHOOTING.md) |

---

## 1–2. Uçtan uca ekran ve deprecated API’ler

**Yapılan değişiklikler:**
- `MainActivity.kt`: `enableEdgeToEdge()`
- `styles.xml`: `windowOptOutEdgeToEdgeEnforcement` ve `android:statusBarColor` kaldırıldı
- `App.tsx`: Android’de `StatusBar` `backgroundColor` kullanılmıyor
- `ConfirmationModal.tsx`: Android’de `StatusBar` / `statusBarTranslucent` kapatıldı (overlay yeterli)
- `AppNavigator.tsx`: Tab bar `useSafeAreaInsets().bottom` ile gesture bar uyumu
- `SafeAreaProvider` zaten mevcut

**Not:** `react-native-screens` ve React Native `StatusBarModule` hâlâ kütüphane içinde deprecated API çağırabilir. Play uyarısı tamamen kaybolmazsa `react-native-screens` güncellemesi gerekebilir.

**Test:** Android 15 emülatör veya cihazda üst/alt çentik, klavye ve modal’ları kontrol edin.

---

## 3. Büyük ekran ve yön kısıtı

**Yapılan:** `AndroidManifest.xml` içindeki `android:screenOrientation="portrait"` kaldırıldı; `app.json` `orientation: "default"`.

**Test:** Tablet veya katlanabilir emülatörde layout kırılmalarını kontrol edin.

---

## 4. 16 KB bellek sayfası

**Yapılan:**
- `ndkVersion` → `28.0.12674087` (16 KB hizalı NDK derlemesi)
- `expo.useLegacyPackaging=false` (sıkıştırılmamış native lib + AGP 8.5.1+ zip hizalaması)
- Fresco **3.6.0** zorlaması (`libgifimage.so`, `libstatic-webp.so` — RN Image GIF/WebP)
- Kullanılmayan `victory-native` kaldırıldı (Skia autolink riski)

**Teşhis:** Play yalnızca **arm64-v8a** (64-bit) `.so` dosyalarını kontrol eder. NDK 28 tek başına yetmez; önceden derlenmiş SDK native parçaları (Fresco vb.) ayrıca güncellenmelidir.

**Doğrulama (release öncesi):**

```powershell
cd android
.\gradlew.bat clean bundleRelease
cd ..
powershell -ExecutionPolicy Bypass -File .\scripts\check-android-16kb.ps1
```

Script tüm `arm64-v8a` kütüphanelerinde ELF LOAD hizasının ≥ 16384 byte olduğunu doğrular. İsteğe bağlı: Android Studio → **Build → Analyze APK** → `lib/arm64-v8a` → **Alignment** sütunu.

**Play Console:** Release → **App Bundle Explorer** → sürüm → **Memory page size**.

**Not:** `zipalign` komutu `.aab` dosyaları için uygun değildir; AAB için `bundletool dump config` veya yukarıdaki script kullanın.

---

## 5. AdMob app-ads.txt

**Önemli:** AdMob, Play’de geliştirici sitesi alt yol olsa bile **alan adı kökünde** dosya arar:

- Doğru (AdMob): `https://umaycorporation.github.io/app-ads.txt`
- Yetersiz (yalnızca alt yol): `https://umaycorporation.github.io/fingofy-legal-pages/app-ads.txt`

### Adım A – Kök GitHub Pages (zorunlu, yapıldı)

Repo: `umaycorporation/umaycorporation.github.io` — kökte `app-ads.txt` yayında.

Tarayıcıda doğrulayın: https://umaycorporation.github.io/app-ads.txt

### Adım B – Play Console

**Geliştirici web sitesi** (gizlilik/yasal sayfalar için) şu şekilde kalabilir:

```
https://umaycorporation.github.io/fingofy-legal-pages/
```

Kök adrese çevirmek zorunlu değil; AdMob yine de kök `app-ads.txt` kullanır.

### Adım C – AdMob (sizin yapmanız gereken)

1. [AdMob](https://apps.admob.com/) → Uygulamalar → **Fingofy (Android)**
2. **app-ads.txt** → **Güncellemeleri kontrol et** / doğrulamayı yeniden çalıştırın
3. Birkaç dakika – 48 saat sürebilir

**Bu repodaki referanslar:**
- `docs/app-ads.txt` (kaynak içerik)
- `store-assets/metadata/app-ads.txt`
- `constants/storePublishing.ts` → `appAdsTxt` kök URL

---

## Yeni release için kontrol listesi

- [ ] `version.json` → yeni `versionCode`
- [ ] `npm run sync-version`
- [ ] `cd android && .\gradlew.bat clean bundleRelease`
- [ ] `npm run check:android-16kb` (16 KB ELF doğrulaması)
- [ ] Play Console’da geliştirici web sitesi dolu
- [ ] `app-ads.txt` erişilebilir
- [ ] Production veya Open testing’e yeni AAB yükle
