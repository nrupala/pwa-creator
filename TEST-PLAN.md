# Test plan — pwa-creator (Zerok Container)

## Scope

Testing covers the Python encrypted-blob core (`zerok/` — blob encrypt/decrypt
round-trips, blob-id behaviour, nonce randomness), and the Android debug build
of the thin app shell (`android/`, `io.zerok.container.MainActivity`) that this
repository's APK pipeline produces. The PWA surface in `pwa-creator/`
(`index.html`, `app.js`, `sw.js`, `manifest.json`) has no automated tests
today; it is covered by CodeQL static analysis and manual exercise. The GUI
(`gui/app.py`) and the storage server (`server/app.py`) are likewise not
covered by automated tests today; they are exercised manually per
`initial_use.md`.

## Suites

| Suite | Covers | Run it | Runs in CI |
|---|---|---|---|
| pytest suite (`tests/test_blob_integrity.py`, `tests/test_invariant.py`) | XChaCha20-Poly1305 encrypt/decrypt round-trips via `zerok.crpyto`, blob encrypt/decrypt and blob-id non-determinism via `zerok.blob` | `pip install cryptography blake3 argon2-cffi requests pytest && python -m pytest tests/ -q` (module form, so the repo root is on `sys.path` and the `zerok` package imports) | **No — see the known defect below; the suite is run locally per CONTRIBUTING.md until the product fix lands** |
| Android debug build | Manifest merge, resource/link and compile of the Android shell at compileSdk/targetSdk 34 | `cd android && ./gradlew assembleDebug` (CI downloads Gradle 8.2 directly) | Yes — `Android APK Build` workflow (`.github/workflows/main.yml`) |
| CodeQL static analysis | Python, Java/Kotlin and JavaScript/TypeScript surfaces | GitHub CodeQL | Yes — `CodeQL` workflow (`.github/workflows/codeql.yml`), push/PR + weekly |

## Known defect (product lane, found 2026-11-06)

The pytest suite cannot currently collect end-to-end: `zerok/crpyto.py`
imports `XChaCha20Poly1305` from
`cryptography.hazmat.primitives.ciphers.aead`, a name that does not exist in
the `cryptography` package (verified against cryptography 50.0.2 and in the
sibling apk-creator repo's CI; the package provides `ChaCha20Poly1305`).
Collection reaches this import today because the earlier defect in front of
it — `zerok/blob.py` importing a nonexistent `.crypto` module instead of
`.crpyto` — was fixed on 2026-11-06. Choosing the replacement cipher
construction changes the product's encrypted-blob format, so it is a
maintainer decision, not a CI fix. Wire the suite into CI when the crypto
fix lands (the workflow shape proven in the sibling repo is recorded in the
OpenSSF engineering-leg log).

## CI gates

- `Android APK Build` green on every push to `main` and on `v*` tags; the
  workflow surfaces the Gradle "What went wrong" section as a check annotation
  on failure so the reason is readable from the run.
- CodeQL runs on push/PR and weekly; findings are dispositioned (fixed, or
  dismissed with a specific reason) on the house SLA.
- The pytest suite is expected green locally before merge per
  CONTRIBUTING.md — currently blocked by the known defect above.

## Cadence

- Every push / pull request: Android APK build, CodeQL.
- Weekly: CodeQL scheduled run (Monday 05:23 UTC).
- Every release candidate: the release-readiness run (checker + evidence
  reads), per the house Release-Readiness Standard.

## Coverage bar

No numeric floor; new functionality lands with tests per CONTRIBUTING.md.
