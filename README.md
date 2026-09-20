# Movie site (Android WebView app)

A minimal Android app that opens **https://filmsoo.xyz/** inside the app (via `WebView`), with:

- **Ad blocking** — requests to a built-in list of ~60 known ad/tracker domains
  (Google ad networks, PropellerAds, PopAds, ExoClick, Adsterra, Taboola,
  Outbrain, and similar) are silently blocked before they load
- **Popup/popunder blocking** — `window.open()` calls (the most common way
  streaming sites launch ad popups) are disabled entirely
- Pull-to-refresh
- A loading progress bar
- In-app back navigation (Android back button steps through page history before exiting)
- JavaScript + DOM storage enabled (needed by most video/streaming sites)
- HTTPS-only network security config

Ad blocking here works at the app/network-request level (like a lightweight
uBlock Origin), not by hiding elements after the page loads — so blocked ads
generally never appear at all rather than flashing and disappearing. It won't
catch every single ad network (streaming sites rotate these constantly), but
it should stop the large majority, especially popups/popunders and banner
ads from mainstream ad networks. If you find a specific ad still getting
through, tell me its domain (visible in browser dev tools) and I'll add it
to the blocklist in `AdBlocker.kt`.

## Getting the installable APK — Option A: Build it in the cloud (no Android Studio needed)

This project includes a GitHub Actions workflow (`.github/workflows/build-apk.yml`)
that builds the APK for you automatically. Steps:

1. Create a new **public or private** repo on [github.com](https://github.com) — name it whatever you like (e.g. `movie-site-app`). Don't add a README/gitignore when creating it, since this project already has them.
2. On your computer, unzip this project, open a terminal inside the `MovieSite` folder, and run:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
   git push -u origin main
   ```
3. On GitHub, open your repo's **Actions** tab. The "Build APK" workflow should already be running (pushing triggers it automatically). If it isn't, click **Build APK → Run workflow**.
4. Wait for the green checkmark (~3–5 minutes).
5. Click into the finished run, scroll to **Artifacts**, and download **MovieSite-debug-apk** — that's a zip containing `app-debug.apk`.
6. Transfer that `.apk` to your Android phone (email it to yourself, upload to Drive, etc.), open it on the phone, and tap to install. You'll need to allow "install unknown apps" for whichever app you used to open it — Android will prompt you for this the first time.

This produces a **debug** build, which is fine for installing on your own phone but isn't signed for the Play Store. If you ever want to publish it, that needs a release signing key — let me know and I can walk you through it.

## Getting the installable APK — Option B: Build locally with Android Studio

I can't compile the `.apk` myself — this sandbox has no Android SDK or
internet access, which building an Android app requires. If you'd rather
not use GitHub, building it yourself in Android Studio is a couple of
clicks, no coding needed:

1. Install **Android Studio** (free, from developer.android.com) if you don't have it.
2. Choose **Open** and select this `MovieSite` folder (the one containing `settings.gradle`).
3. Let Gradle sync (first sync downloads dependencies — needs internet).
4. **Build → Build Bundle(s)/APK(s) → Build APK(s)**.
5. Click the **locate** link in the notification that pops up (or find it at
   `app/build/outputs/apk/debug/app-debug.apk`) and copy that file to your
   phone to install.

## Changing the site later

Open `app/src/main/java/com/example/moviesite/MainActivity.kt` and edit the `homeUrl` value near the top.

## Notes

- The app has no browser chrome (no address bar) — it's a single-site viewer, not a general browser. If you want an address bar, tab support, or bookmarks, that's a bigger build — let me know and I can add it.
- Some sites block WebView user agents or require pop-ups/downloads that a plain WebView won't fully support (ads, DRM video, file downloads). If filmsoo.xyz uses heavy ad/popup redirects, you may see those inside the app since there's no ad blocking built in.
- App uses `applicationId = com.example.moviesite` — change this in `app/build.gradle` before publishing anywhere, so it doesn't clash with other apps.
