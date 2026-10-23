# MANIFEST — v1.2

Only **OpenCode A1** changed in 1.2. **Ghostty A1 is unchanged** and still ships
its 1.1 build from the `v1.1` release; **Run-Karl-Run** is unchanged at 1.0. Do
not treat the absence of those assets here as a withdrawal.

## Files

| file | package | version | size | sha256 |
|---|---|---|---|---|
| `OpenCodeA1-1.2-ai.a1143.opencode.apk` | `ai.a1143.opencode` | 1.2 (3) | 68,313 B | `813874fa7c282594a33b8014e0c16fb3b82717d50dea92a6e767ae24350c5d98` |

The hash is of the file installed on the SM-S266V: `adb pull` of the installed
APK, compared against the build output and against this asset — all three
identical, not merely the same size.

## What changed in 1.2

**A message sent from the chat composer could be typed into the terminal.**

Android's IME keeps a composition alive across a focus change. Sending a message
clears the composer and moves focus to the terminal, but nothing cancels the
composition — so the commit lands in whichever editable has focus now, which is
the terminal's hidden `textarea`. Measured on the SM-S266V on 2026-10-22: an
`Enter` at 07:42:10, followed 2 ms later by a single `beforeinput`/`insertText`
carrying the entire 413-character message, targeted at `TEXTAREA[Terminal input]`.

At the event level those bytes are indistinguishable from real typing —
`inputType: insertText`, `isComposing: false` — so the app cannot tell them apart
by shape. What it can do is remember where the text came from. The injected
script now records, from any *non-terminal* editable, two independent signals:
the composition that was live there, and the text the field held when `Enter`
was pressed on it. A commit into the terminal that matches either within 1200 ms
is dropped once.

Deliberately narrow: single characters are never suppressed, so a real keystroke
cannot be eaten; containment matching is restricted to strings of 8+ characters;
and the terminal's own composition path is untouched.

### What is verified, and what is not

| check | result |
|---|---|
| harness `test/keyboard` (13 checks) | **13/13** |
| same, with the guard made a no-op (`MUTANT=3`) | **11/13** — the message lands in the shell again |
| harness baseline, no script at all | the message *does* arrive at the terminal — the scenario is not vacuous |
| real Samsung IME on the SM-S266V | **composer send no longer reaches the terminal** |

Not verified: other IMEs and other devices. A keyboard that delivers the commit
later than 1200 ms, or splits it into pieces that match neither signal, can still
slip through. This is a mitigation for a bug in the page's own composition
handling, not a fix to that page — a server upgrade could change the behaviour it
is compensating for.

## Signing

Debug key, `CN=Android Debug`, certificate SHA-256
`103d8ea0683f4c5c6e4010d858753a193b7809e24810804bced01ec0c729ed25` — **the same
key as v1.0 and v1.1**, so it installs over them as an update on any phone that
already has one. It will not install over a build signed by a different machine's
debug key; uninstall first in that case.

`android:debuggable` is true, as in earlier builds. That is what makes
`adb shell run-as ai.a1143.opencode` work and is load-bearing for the terminal
test harness.
