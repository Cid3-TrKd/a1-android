# A1 Android apps

Three small Android apps built for, and verified on, a Samsung **SM-S266V**
(Galaxy A26 5G, Android 16). Two are the phone-side half of the A1-143 setup: a
terminal that runs on the phone itself, and a window onto the OpenCode agent that
runs on the laptop. The third is a cat.

| app | package | what it is | needs |
|---|---|---|---|
| **Ghostty A1** | `ai.a1143.ghostty` | a real terminal — Ghostty's VT engine (WASM) over a native `forkpty` PTY, in a WebView | nothing. self-contained |
| **OpenCode A1** | `ai.a1143.opencode` | a wrapper around the OpenCode v2 web UI | an OpenCode server **you** run, and a pairing link from it |
| **Run-Karl-Run** | `ai.a1143.runkarlrun` | a fat cat in the status bar whose gait follows real CPU load, plus a live per-core readout | nothing. asks for no network permission at all |

Download all three from the [Releases](../../releases) page.

## Install

Ghostty A1 and OpenCode A1 are **debug-signed** APKs (see
[MANIFEST.md](MANIFEST.md) for hashes and the signing certificate).
Run-Karl-Run is signed with a **release key** and has its own manifest,
[MANIFEST-runkarlrun-1.0.md](MANIFEST-runkarlrun-1.0.md). That means:

- Android will ask you to allow installs from the app you are using to open the
  file. That is the normal sideload prompt, not a warning about this app.
- Play Protect may show a scan prompt. The build has not been through Play.
- They are **not** on any store, and there is no update channel. A newer build
  has to be installed over the old one, and only if it was signed with the same
  key — see the keystore notes in the two manifests.

Requires Android 6.0 (API 23) or newer for the two A1 apps, and **Android 8.0
(API 26)** for Run-Karl-Run. Ghostty A1 ships an **arm64-v8a** native library, so
it needs a 64-bit ARM device — i.e. essentially every phone since ~2016, but not
an x86 emulator. Run-Karl-Run has no native libraries.

Sizes: Ghostty A1 is ~650 KB, OpenCode A1 is ~72 KB, Run-Karl-Run is ~375 KB.

## What each one talks to — and what it does not

This is the part worth being precise about, because "an app that connects to a
terminal" can mean very different things.

**Run-Karl-Run cannot make a network connection: it does not request the
`INTERNET` permission.** Its permissions are `POST_NOTIFICATIONS`,
`FOREGROUND_SERVICE` and `FOREGROUND_SERVICE_SPECIAL_USE` — nothing else. There
is no analytics SDK and no update check, so there is nowhere for data to go even
if someone later wanted it to.

**Ghostty A1 makes no network connections at all.** The shell is a process on the
phone, reached through a pty; the WASM engine renders it. It requests the
`INTERNET` permission only because it serves its own bundled assets to the
WebView over a loopback HTTP server bound to `127.0.0.1` on a random port with a
random path token. Nothing leaves the device.

**OpenCode A1 has no server address, no token and no key inside it.** It ships
with an empty default and asks on first run. It can only reach a server whose
owner handed over either:

- a pairing link from `opencode pair` — single-use, expires in 5 minutes, sets a
  30-day cookie; or
- the server address plus its password.

So a copy of this APK installed by a stranger points at that stranger's own
server and nobody else's. It cannot reach back to the machine these were built
on: the only address that ever appeared in a build was removed before publishing,
and the API answers `401` without a valid session cookie.

## Caveats, stated plainly

- Built and tested on one device (SM-S266V, Android 16). Other Android versions
  and OEM skins are untested.
- Run-Karl-Run is republished here unchanged from its own repository; if the two
  ever disagree, that release is the authoritative copy.
- Signed with keys from the machine these were built on (debug keystore for the
  two A1 apps, a dedicated release key for Run-Karl-Run). If you rebuild any of
  them elsewhere, the signature will differ and Android will refuse to install
  over the top; uninstall first.
- OpenCode A1 stores two things: the server origin and the WebView's cookie jar.
  Nothing else. No conversation data is cached on the phone — the agent runs on
  the server.
- Ghostty A1 is a terminal, so it can run anything the phone's shell user can.
  That is the point of it, but it is worth saying out loud.

Source lives on the machine these were built on and is not published here yet.
