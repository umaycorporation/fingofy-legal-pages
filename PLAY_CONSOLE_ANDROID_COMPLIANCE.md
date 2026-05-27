# Play Console Android Uyumluluk Rehberi

Bu doküman Play Console’da görünen Android 15/16 uyarıları ve AdMob `app-ads.txt` doğrulaması için adım adım kontrol listesidir.

## Özet

| # | Konu | Durum (repo) | Sonraki adım |
|---|------|--------------|--------------|
| 1 | Uçtan uca ekran (SDK 35) | `enableEdgeToEdge()`, opt-out kaldırıldı | Yeni build + cihazda test |
| 2 | Deprecated status/navigation bar API | `statusBarColor` kaldırıldı, JS `backgroundColor` Android’de kapalı | Yeni build |
| 3 | Büyük ekran / yön kısıtı | `screenOrientation` manifest’ten kaldırıldı | Tablet/emülatörde test |
| 4 | 16 KB page size | NDK 28, `useLegacyPackaging=false` | Yeni AAB yükle |
| 5 | AdMob app-ads.txt | `docs/app-ads.txt` hazır | Legal-pages repo + Play alanları |

---

## 1–2. Uçtan uca ekran ve deprecated API’ler

**Yapılan değişiklikler:**
- `MainActivity.kt`: `enableEdgeToEdge()`
- `styles.xml`: `windowOptOutEdgeToEdgeEnforcement` ve `android:statusBarColor` kaldırıldı
- `App.tsx`: Android’de `StatusBar` `backgroundColor` kullanılmıyor
- `SafeAreaProvider` zaten mevcut

**Not:** `react-native-screens` ve React Native `StatusBarModule` hâlâ kütüphane içinde deprecated API çağırabilir. Play uyarısı tamamen kaybolmazsa `react-native-screens` güncellemesi gerekebilir.

**Test:** Android 15 emülatör veya cihazda üst/alt çentik, klavye ve modal’ları kontrol edin.

---

## 3. Büyük ekran ve yön kısıtı

**Yapılan:** `AndroidManifest.xml` içindeki `android:screenOrientation="portrait"` kaldırıldı; `app.json` `orientation: "default"`.

**Test:** Tablet veya katlanabilir emülatörde layout kırılmalarını kontrol edin.

---

## 4. 16 KB bellek sayfası

**Yapılan:** `ndkVersion` → `28.0.12674087` (16 KB hizalı derleme).

**Yeni sürüm gerekli:** Mevcut production build (versionCode 19) bu değişiklikleri içermez. Yeni `versionCode` ile AAB alıp yükleyin.

**Doğrulama (opsiyonel, lokal):**
```bash
zipalign -c -P 16 -v 4 android/app/build/outputs/bundle/release/app-release.aab
```

---

## 5. AdMob app-ads.txt

**Sorun:** Play Console’da “geliştirici web sitesi bulunamadı” → `app-ads.txt` doğrulanamıyor.

### Adım A – Play Console (zorunlu)

1. [Google Play Console](https://play.google.com/console) → Uygulama → **Mağaza varlığı** → **Ana mağaza girişi**
2. **Geliştirici web sitesi** alanına tam olarak şunu yazın:
   ```
   https://umaycorporation.github.io/fingofy-legal-pages/
   ```
3. Kaydedin (sonunda `/` olması önemli).

### Adım B – GitHub Pages (fingofy-legal-pages repo)

1. `umaycorporation/fingofy-legal-pages` reposuna gidin
2. Kök dizine `app-ads.txt` ekleyin (içerik):

   ```
   google.com, pub-8034363135895920, DIRECT, f08c47fec0942fa0
   ```

3. GitHub Pages’in `main` veya `gh-pages` branch’ten yayınlandığını doğrulayın
4. Tarayıcıda açın: https://umaycorporation.github.io/fingofy-legal-pages/app-ads.txt

### Adım C – AdMob

1. AdMob → Uygulamalar → Fingofy (Android)
2. **app-ads.txt** doğrulamasını yeniden çalıştırın
3. Yayılım 24–48 saat sürebilir

**Bu repodaki referanslar:**
- `docs/app-ads.txt`
- `store-assets/metadata/app-ads.txt`
- `constants/storePublishing.ts`

---

## Yeni release için kontrol listesi

- [ ] `version.json` → yeni `versionCode` (ör. 20)
- [ ] `npm run sync-version`
- [ ] Release AAB al
- [ ] Play Console’da geliştirici web sitesi dolu
- [ ] `app-ads.txt` erişilebilir
- [ ] Production veya Open testing’e yeni AAB yükle
