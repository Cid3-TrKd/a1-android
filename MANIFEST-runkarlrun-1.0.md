# MANIFEST — Run-Karl-Run 1.0

One artifact, republished here so all three A1 phone apps live in one place. It is
**not a new build**: the bytes are identical to the release already published in
[Cid3-TrKd/Run-Karl-Run](https://github.com/Cid3-TrKd/Run-Karl-Run/releases/tag/v1.0).

## File

| file | package | version | size | sha256 |
|---|---|---|---|---|
| `RunKarlRun-1.0.apk` | `ai.a1143.runkarlrun` | 1.0 (1) | 384,395 B | `3ae0dd865c04da687e49aa7f94af79c7b12bd8adf304ea1544239385ce081fa8` |

Same hash as the asset in the Run-Karl-Run repo, whose own `SHA256SUMS` lists it.

## How "identical" was established

Not by filename and not by version string — by content:

- anonymous download of the existing public asset → `sha256` equal to the file
  uploaded here (both `3ae0dd86…ce081fa8`);
- **all 43 zip entries byte-identical** between the two copies, so no code,
  resource, manifest or native library differs;
- same package `ai.a1143.runkarlrun`, same `versionCode 1` / `versionName 1.0`.

A local build in `dist/` on the authoring machine is 384,395 B too but hashes
differently (`949bf7f7…3e2f468`). It is the *same app*: the differing 1,166 bytes
sit only in the APK signing block, and every one of the 43 entries matches. It
was re-signed after the release was cut. The published bytes are treated as
canonical here, because they are what the public already has and what the
project's own `SHA256SUMS` records.

## Signing — a different key from the other two apps in this repo

| app | key |
|---|---|
| **Run-Karl-Run** | **release key**, RSA 4096, cert SHA-256 `1B:9D:AD:C4:50:39:6F:5A:FA:BE:BD:BB:7F:5E:BA:6D:62:8D:D8:11:D6:30:C7:15:6A:9E:70:93:26:AC:F6:BC` |
| Ghostty A1, OpenCode A1 | this machine's Android **debug** keystore |

`apksigner verify --print-certs` reports `V3.0 Signer` with that digest, and the
APK verifies cleanly.

Practical consequence: an update to Run-Karl-Run must be signed with the same
release key, or Android will refuse to install it over the top. A debug-signed
rebuild is a *different app identity* from Android's point of view — uninstall
first, which loses the app's stored state. The release key is described in
`SIGNING.md` in the Run-Karl-Run repo.

## What it is

A fat cat that lives in the status bar and runs faster as the phone's CPU works
harder, plus a live overall and per-core CPU readout in the app. No ads, no
trackers, and **no `INTERNET` permission at all** — the APK cannot reach the
network, so there is nothing it could send anywhere. Requested permissions are
only `POST_NOTIFICATIONS`, `FOREGROUND_SERVICE` and
`FOREGROUND_SERVICE_SPECIAL_USE`; the first two are what let the cat run in the
status bar at all.

## Requirements

- **minSdk 26** (Android 8.0), **targetSdk 36**, built against Android 16.
  Verified on the SM-S266V running Android 16 (`BP4A.251205.006.S266VUDSACZH3`).
- Notifications are optional, but the status-bar cat is a foreground-service
  notification — without the permission the cat is not drawn. Android 13+ asks
  at first launch.
- Sideloaded, not from any store. Play Protect may show a scan prompt; the build
  has not been through Play.
