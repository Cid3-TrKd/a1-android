# MANIFEST — Run-Karl-Run 1.2

A real new build, not a republish. The 1.0 bytes in this repo still work, but 1.2
is the version that fixes the two bugs that made the cat walk at full load:

- the art was not a run cycle (both leg pairs driven by one `sin(phase)`, so
  frame 6 was frame 0) — now twelve poses, two six-pose strides with the lead legs
  swapped;
- the top gear was a 0.9 s stride, which is a walking cadence — now a continuous
  map from load (2.2 s idle → 0.45 s pegged).

The pose is derived from elapsed time rather than counted out per post, so the
stride is exact and a dropped update self-corrects instead of leaving the
animation permanently behind.

## File

| file | package | version | size | sha256 |
|---|---|---|---|---|
| `RunKarlRun-1.2.apk` | `ai.a1143.runkarlrun` | 1.2 (3) | 392,917 B | `82dc73882a7ef0bb9f2a6c49af5b399a9d20c9a248dafc1179ed86a99660f5c9` |

`sha256sum -c SHA256SUMS.runkarlrun-1.2` verifies it.

## Signing — the same key as 1.0

`apksigner verify --print-certs` reports a `V3.0 Signer` with certificate
SHA-256 digest

```
1B:9D:AD:C4:50:39:6F:5A:FA:BE:BD:BB:7F:5E:BA:6D:62:8D:D8:11:D6:30:C7:15:6A:9E:70:93:26:AC:F6:BC
```

RSA 4096, `CN=Run-Karl-Run, OU=A1-143, O=cid3trk, C=US`, valid 2026–2056. It
is the same key as `runkarlrun-v1.0`, so 1.2 installs over the top of 1.0
without an uninstall. If the digest is not exactly the line above, the file was
not signed by this project and should not be installed.

## Measured on the phone

Samsung SM-S266V, Android 16, notification counters before and after:

| state | gait | enqueued | delivered | shed |
|---|---|---|---|---|
| idle | walk | 1.64/s | 1.64/s | 0 |
| pegged (8 × `yes`) | bolt | 3.35/s | 3.24/s | 3.2% |
| 5 s after release | walk | 1.63/s | 1.63/s | 0 |

The screen itself agrees: recording the status bar with `screenrecord` at full
load and reading the compositor's own frames gives a median pose change of 300 ms
cycling three poses — a 900 ms loop, two strides, so **450 ms per stride as
rendered**. Sixteen readings across a load ramp all matched their own gait band,
so the percentage in the text and the cat's gait can no longer disagree.

## What it needs

- Android 8.0 / API 26 or newer, `targetSdk 36`. Verified on a Samsung SM-S266V
  running Android 16 (`BP4A.251205.006.S266VUDSACZH3`).
- `POST_NOTIFICATIONS` is required; Android 13+ asks on first launch.
- No native libraries, so any ABI works.
- Sideloaded, not from a store: expect the normal "install from this source"
  prompt, and possibly a Play Protect scan notice. The build has not been through
  Play.

## Permissions

`POST_NOTIFICATIONS`, `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_SPECIAL_USE`. No
`INTERNET`, no analytics, no update check.

## Relation to 1.0

1.2 supersedes 1.0 (it is the same app, a strictly better version, signed with
the same key), but 1.0 is kept published as the record of the original release.
If the two ever disagree about the app's behaviour, 1.2 is the current truth.
