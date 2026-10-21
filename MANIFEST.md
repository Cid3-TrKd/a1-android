# MANIFEST

Exact artifacts. The hashes below are of the files installed on the SM-S266V at
the time of publication — verified by `adb pull` of the installed APK and
comparing against the build output, not by trusting the file name.

## Files

| file | package | version | size | sha256 |
|---|---|---|---|---|
| `GhosttyA1-1.0-ai.a1143.ghostty.apk` | `ai.a1143.ghostty` | 1.0 (1) | 662,595 B | `ba102c9459dd62a20f6cc21cedc03c5746920f370cc3b648bbadad216bfca49e` |
| `OpenCodeA1-1.0-ai.a1143.opencode.apk` | `ai.a1143.opencode` | 1.0 (1) | 67,573 B | `e66d0ed0a54480ddd3af85e9f4367be33c296ab47522790133d52a88eaec713d` |

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
| Ghostty A1 | `ai.a1143.ghostty` launches, PTY works, shell renders, keyboard input verified byte-for-byte |
| OpenCode A1 | `ai.a1143.opencode` launches, pairs, loads the web UI, dark theme |

## What is inside

**Ghostty A1** — 662,595 B

    assets/ghostty-vt.wasm      639,033 B   Ghostty's VT engine (MIT)
    assets/ghostty-web.umd.js   939,676 B   coder/ghostty-web @ 513463a
    assets/a1term.js             19,426 B   the app's own front-end
    assets/index.html             2,909 B
    lib/arm64-v8a/liba1pty.so    13,008 B   native forkpty() PTY, NDK r21

arm64-v8a only. No network destinations of any kind are present in the APK; the
`INTERNET` permission is used solely for the loopback asset server.

**OpenCode A1** — 67,573 B, no native libraries, pure Java + WebView.

No server address, token, cookie or private key is present in the APK. Verified
by unpacking the built artifact and scanning the dex and resources for the
tether address, the forward port and a session cookie name — all absent.

## Provenance note

Neither project directory is a git repository, so there is no commit to cite.
The artifacts above *are* the record: these are the exact bytes that ran on the
device, identified by hash. If the source is published later, its build output
should reproduce the hashes in this file.
