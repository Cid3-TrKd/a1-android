# MANIFEST

Exact artifacts. The hashes below are of the files installed on the SM-S266V at
the time of publication — verified by `adb pull` of the installed APK and
comparing against the build output, not by trusting the file name.

## Files

| file | package | version | size | sha256 |
|---|---|---|---|---|
| `GhosttyA1-1.1-ai.a1143.ghostty.apk` | `ai.a1143.ghostty` | 1.1 (2) | 650,466 B | `7ae3fd7ba856c7cf923c6f4714b310efb51343f69697873d32d28bf2da74578b` |
| `OpenCodeA1-1.1-ai.a1143.opencode.apk` | `ai.a1143.opencode` | 1.1 (2) | 71,887 B | `f83880f80fd41b24d5f16785d5c47b2e97668a4b7a0b8b666a474a77f7b286a0` |

The 1.0 builds from 2026-10-21 are still attached to the `v1.0` release; they are
superseded, not deleted, and their hashes stay in that release's notes.

## What changed in 1.1

**Ghostty A1 — the soft keyboard did not appear on a cold start.** The Activity
never asked the `WebView` for focus, so `document.hasFocus()` stayed `false` while
`document.activeElement` was already the terminal's hidden `textarea`. Chromium
suppresses the IME for an unfocused page, so tapping the terminal focused the DOM
element and raised nothing — the app looked untypable until some unrelated event
(a hardware key, a rotation) handed focus over. Fixed with
`setFocusable`/`setFocusableInTouchMode`/`requestFocus()` when the view is added
and again in `onWindowFocusChanged`.

Measured on the SM-S266V, cold start each time:

| build | `hasFocus` before any interaction | tap → IME |
|---|---|---|
| 1.0 | `false` | `mInputShown=false` |
| 1.1 | **`true`** | **`mInputShown=true`** |

and after the tap, `echo ghostty-ok` typed on the on-screen keyboard ran and
printed `ghostty-ok`. Note the diagnostic that isolated it: sending one
`KEYCODE_TAB` flipped `hasFocus` to `true` and the very next tap opened the
keyboard.

**OpenCode A1 — no compiled-in server address.** 1.0 had
`DEFAULT_ORIGIN = "http://10.183.60.139:49375"` as the seed for the address
dialog: one machine's DHCP tethered address, published to the internet, and
useless (or misleading) on anyone else's phone. Removed. The app now ships with
no address at all and asks on first run, which is also what keeps it from pointing
at a dead one.

Both APKs also carry the 2026-10-22 keyboard work from the working tree: the
WebView-IME injection (`a1-keyboard.js`) in OpenCode A1, and `fitsSystemWindows`
insets on every activity root.

`SHA256SUMS` in this directory is the machine-readable form:

    sha256sum -c SHA256SUMS

## Build

| | |
|---|---|
| minSdk | 23 (Android 6.0) |
| targetSdk / compileSdk | 36 / 36.1 (Android 16) |
| Gradle | 8.13 |
| Android Gradle Plugin | 8.13.2 |
| JDK | OpenJDK 21.0.12.1 |
| build type | `debug` |

## Signing

Both APKs are signed with this machine's **Android debug keystore**
(`~/.android/debug.keystore`), scheme v1 + v2:

    CN=Android Debug, O=Android, C=US
    SHA-256: 103d8ea0683f4c5c6e4010d858753a193b7809e24810804bced01ec0c729ed25

Consequences worth knowing before you install:

- These are debug builds. `android:debuggable` is true, which is also what makes
  `adb shell run-as ai.a1143.*` work — useful on this setup, and not something a
  release build would allow.
- Android will only accept an update over the top if it is signed by the **same
  key**. The debug key lives on the machine that built these; a rebuild
  elsewhere produces a different key and requires an uninstall first.
- Do not publish the debug keystore. It is a private key, even though its
  password (`android`) is public knowledge.

## Verified on

| | |
|---|---|
| device | Samsung SM-S266V (Galaxy A26 5G), Exynos 1380 |
| Android | 16, build `BP4A.251205.006.S266VUDSACZH3` (CSC TFN) |
| Ghostty A1 | `ai.a1143.ghostty` launches, PTY works, shell renders, **the on-screen keyboard opens on the first tap after a cold start**, and a typed command runs (1.1) |
| OpenCode A1 | `ai.a1143.opencode` launches, pairs over HTTPS, loads the web UI, dark theme (1.1) |

## What is inside

**Ghostty A1** — 650,466 B

    assets/ghostty-vt.wasm      639,033 B   Ghostty's VT engine (MIT)
    assets/ghostty-web.umd.js   939,676 B   coder/ghostty-web @ 513463a
    assets/a1term.js             19,426 B   the app's own front-end
    assets/index.html             2,909 B
    lib/arm64-v8a/liba1pty.so    13,008 B   native forkpty() PTY, NDK r21

arm64-v8a only. No network destinations of any kind are present in the APK; the
`INTERNET` permission is used solely for the loopback asset server.

**OpenCode A1** — 71,887 B, no native libraries, pure Java + WebView.

No server address, token, cookie or private key is present in the APK. Verified
by unpacking the built artifact and scanning the dex and resources for the
tether address, the forward port and a session cookie name — all absent.

## Provenance note

Neither project directory is a git repository, so there is no commit to cite.
The artifacts above *are* the record: these are the exact bytes that ran on the
device, identified by hash. If the source is published later, its build output
should reproduce the hashes in this file.
