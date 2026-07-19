# WISP Field

Thin Capacitor Android shell for the WISP Central field-worker app. It loads
the live hosted dashboard (`https://hansanet.in/app` via `server.url` in
[capacitor.config.json](capacitor.config.json)) — all product UI lives in the
main repo's `web/`; this repo is only the native wrapper. Workers sign in with
a `worker` role account (created in Settings → Login accounts) and get the
scoped triage view.

Why a native shell at all: upcoming phases need capabilities a mobile browser
can't provide — native push notifications (P2) and live on-shift background
GPS tracking (P4). Those land as small `Capacitor.isNativePlatform()`-guarded
bridge code in the main repo's `web/`, plus plugins here.

## Releasing

Tag `vX.Y.Z` and push — GitHub Actions builds a **signed** APK and attaches it
to the release (`wisp-field.apk` + `SHA256SUMS`). Central mirrors the latest
release to the stable URL workers install from:

    https://hansanet.in/download/app/wisp-field.apk

`versionCode` is stamped from the Actions run number (monotonic, required for
in-place upgrades); `versionName` from the tag.

Required repository secrets (Settings → Secrets and variables → Actions):

- `RELEASE_KEYSTORE_B64` — base64 of the PKCS12 release keystore
- `RELEASE_KEYSTORE_PASSWORD` — its password (key alias is `wispfield`)

The keystore lives OUTSIDE this repo (it is public). Every release must be
signed with the same keystore or phones refuse to upgrade in place. Losing it
means workers uninstall/reinstall once under a new key — keep it backed up.

## Install on a phone

Open the download URL, allow "install from unknown sources" when prompted,
install, log in (tick "remember this device"). Minimum Android 6.0.

## Local development

Needs Node 20+, JDK 17+, Android SDK. `npm ci && npx cap sync android`, then
open `android/` in Android Studio or `cd android && ./gradlew assembleDebug`.
The webview points at production; change `server.url` locally to test against
a dev central.
