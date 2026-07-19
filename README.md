# MAKDEL ERP — Android App (no Play Store)

A thin native Android wrapper around the live MAKDEL ERP web app
(`https://web.makdel.in`). It's a real installable `.apk` — not a browser
shortcut — with:

- Full-screen WebView, no browser chrome
- Pull-to-refresh
- File upload support (needed for attaching documents/photos)
- Native file downloads (receipts, PDFs, exports) via Android's Download Manager
- Camera permission handling (for QR-code based payment scanning)
- WhatsApp / tel / mailto links open in their native apps
- Offline screen with retry when there's no connection
- Back button navigates web history before exiting
- App icon and dark theme taken directly from your `manifest.json`

It is **not** published to the Play Store — you sideload it directly, same
pattern as MAKSAFE.

## 1. Push this project to GitHub

```bash
cd MakdelERPApp
git init
git add .
git commit -m "Initial MAKDEL ERP Android wrapper"
git branch -M main
git remote add origin https://github.com/<your-username>/makdel-erp-android.git
git push -u origin main
```

Use a **private** repo if you don't want the source public — the Actions
build works the same either way.

## 2. Let GitHub Actions build the APK

The workflow at `.github/workflows/build-apk.yml` runs automatically on every
push to `main`. It:

1. Builds an unsigned debug APK (`assembleDebug`) — no keystore/signing setup needed to get started.
2. Uploads it as a workflow **artifact**.
3. Also attaches it to a GitHub **Release** (tag `build-N`) so it's easy to grab a direct download link on your phone.

To trigger a build without pushing code, go to **Actions → Build APK → Run workflow**.

## 3. Install on your phone

Easiest: open the GitHub **Releases** page on your phone's browser
(`github.com/<you>/makdel-erp-android/releases`), download `app-debug.apk`,
tap it. Android will ask you to allow installs from that source (Chrome or
Files) — allow it once, then install.

No Play Store, no review process, no restrictions on what the app can do.

## 4. Updating the app later

Any push to `main` rebuilds the APK automatically. Bump `versionCode` /
`versionName` in `app/build.gradle` when you want the version number to
change, then just reinstall the new APK over the old one (same
`applicationId`, so your data/session persists).

## Notes on signing (optional, for a "real" release build)

Right now the workflow builds a **debug** APK, which is fine for personal/
internal sideloading indefinitely — Android has no problem running debug
builds forever. If you later want a signed release build (e.g. to distribute
more widely or reduce Play Protect warnings), you'd add a keystore as a
GitHub secret and switch the workflow to `assembleRelease`. Ask if you want
this set up.

## Project structure

```
MakdelERPApp/
├── app/
│   ├── build.gradle              # app module config, package: in.makdel.erp
│   └── src/main/
│       ├── AndroidManifest.xml   # permissions, launcher activity
│       ├── java/in/makdel/erp/MainActivity.kt
│       └── res/                  # icons (from your manifest.json), theme, layout
├── build.gradle                  # project-level
├── settings.gradle
└── .github/workflows/build-apk.yml
```

## Changing the URL

Edit `startUrl` in `MainActivity.kt` (currently
`https://web.makdel.in/index.html`) if you ever move the ERP to a different
domain or want to point at a staging URL first.
