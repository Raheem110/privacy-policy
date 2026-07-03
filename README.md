# AI BG Remover

**On-device AI background removal for Android** — remove, blur, and edit image backgrounds locally. No cloud upload for segmentation. Published by **Raheem Tech**.

[![Min SDK](https://img.shields.io/badge/minSdk-24-blue)](app/build.gradle.kts)
[![Target SDK](https://img.shields.io/badge/targetSdk-36-blue)](app/build.gradle.kts)
[![LiteRT](https://img.shields.io/badge/LiteRT-1.4.2-green)](gradle/libs.versions.toml)

---

## Features

- **One-tap background removal** using on-device AI (U²-Net-P via TensorFlow Lite / LiteRT)
- **Blur background** with adjustable strength
- **Manual editor tools** — erase, brush, crop, rotate, flip, edge feather, wand selection
- **Custom backgrounds** — solid color, gradient, gallery image, transparent
- **Save & share** edited images (JPG, PNG, WEBP)
- **Recent projects** — resume editing (free & VIP tiers)
- **VIP lifetime unlock** — no ads, higher resolution export, more project slots
- **Light & dark theme**
- **Privacy-first** — photos processed on your phone, not uploaded for background removal

---

## Privacy Policy

📄 **[Privacy Policy](docs/privacy-policy.html)**

Questions or support: [abdurraheemk622@gmail.com](mailto:abdurraheemk622@gmail.com)

---

## Tech stack

| Layer | Technology |
|-------|------------|
| Language | Java 11 |
| UI | Material Components, ViewBinding |
| Architecture | MVVM (`ViewModel` + `LiveData` + Repository + Use cases) |
| AI runtime | [Google LiteRT](https://ai.google.dev/edge/litert) 1.4.2 (TFLite) |
| Model | U²-Net-P (`u2netp.tflite` debug / encrypted `ml/seg.pkg` release) |
| Inference | CPU / XNNPACK (4 threads), NNAPI fallback |
| Monetization | Google Play Billing (VIP), Google AdMob + UMP consent |
| Analytics | Firebase Analytics & Crashlytics |
| Security | Encrypted model, EncryptedPrefs, Play Integrity, R8 ProGuard |

---

## Architecture

```
app/src/main/java/com/raheemtech/bgremover/
├── viewmodel/          MainViewModel, EditorViewModel
├── data/repository/    BackgroundRemovalRepository, EditorRepository, ImageRepository
├── domain/usecase/     RemoveBackground, SaveImage, CropImage, etc.
├── processing/         TFLite pipeline, segmentation, blur, brush
├── ui/main/            Activity coordinators & UI binders
├── billing/            PremiumManager, Play Billing
├── ads/                AdController (AdMob)
├── security/           Model encryption, integrity, audit probes
└── di/                 AppContainer (manual DI)
```

**Data flow:** `Activity` → `ViewModel` → `UseCase` → `Repository` → worker thread → `LiveData` → UI.

Heavy work (model load, inference, image I/O, export) runs on a **background executor**, not the main thread.

---

## Requirements

- Android Studio Ladybug or newer
- JDK 11+
- Android SDK 36
- Python 3 (optional — encrypt release model via `encryptSegmentationModel` Gradle task)

---

## Build & run

```bash
# Clone
git clone https://github.com/<your-username>/AIBGRemoverEditor.git
cd AIBGRemoverEditor

# Debug APK (plain model + test ads)
./gradlew assembleDebug

# Release APK (encrypted model + ProGuard — set production AdMob IDs first)
./gradlew assembleRelease
```

Install debug build:

```bash
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

### Before Play Store release

1. Replace placeholder AdMob IDs in `app/build.gradle.kts` (`release` build type).
2. Run model encryption: `./gradlew encryptSegmentationModel`
3. Configure Play Console in-app product: `vip_lifetime`
4. Restrict Firebase API key in Google Cloud Console.
5. Set release signing config in `gradle.properties` / Android Studio.
6. Host `docs/privacy-policy.html` and add URL to Play Console.

See [SECURITY_REPORT.md](SECURITY_REPORT.md) for the full security checklist.

---

## Project structure (key files)

| Path | Purpose |
|------|---------|
| `app/src/main/java/.../processing/TFLiteManager.java` | LiteRT interpreter & delegate setup |
| `app/src/main/java/.../processing/BackgroundSegmentationHelper.java` | Segmentation pipeline |
| `app/src/main/java/.../viewmodel/MainViewModel.java` | Main screen state & processing |
| `app/src/main/assets/u2netp.tflite` | Debug model asset |
| `app/src/main/assets/ml/seg.pkg` | Release encrypted model |
| `docs/privacy-policy.html` | Public privacy policy (GitHub Pages) |
| `scripts/encrypt_model.py` | Model encryption for release |
| `gradle/libs.versions.toml` | Dependency versions |

---

## Free vs VIP

| Feature | Free | VIP |
|---------|------|-----|
| Background removal | ✅ | ✅ |
| Max process / export edge | 1080px | 4096px |
| Inference letterbox edge | 1024px | 1536px |
| Ads | Yes | No |
| Recent projects | 5 | 40 |

---

## Open source components

This app uses open-source libraries including AndroidX, Material Components, LiteRT, U²-Net, ImagePicker, and Android-Image-Cropper. Full license notices are in the app under **Open source licenses**.

---

## License

App code, branding, and encrypted release model packaging are **© Raheem Tech**. Third-party libraries are used under their respective licenses (mostly Apache 2.0). This repository may not include all production secrets (signing keys, production AdMob IDs, model encryption keys).

---

## Support

- **Email:** abdurraheemk622@gmail.com
- **Play Store:** [com.raheemtech.bgremover](https://play.google.com/store/apps/details?id=com.raheemtech.bgremover)

---

## Developer

**Raheem Tech** — AI BG Remover
