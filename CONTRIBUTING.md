# Contributing to Zerok Container (pwa-creator)

## PR flow (the certification discipline)

1. Branch from `main`; open a **draft PR**. PRs are the per-update certification:
   every change is traced, versioned, and reviewed.
2. The owner merges when green. **Never push to `main` directly** (branch
   protection / retired direct pushes where the plan allows; until then,
   green-merge is manual discipline).
3. Every PR adds a `CHANGELOG.md` entry under `## [Unreleased]`.
4. Semver bump with the PR: `patch` for fixes/chores, `minor` for features,
   `major` for breaking changes. Breaking blob-format changes require migration
   tooling per `docs/product-contract.md`.
5. The single version source of truth is the top-level `VERSION` file. The APK
   workflow reads it and names the artifact `zerok-container-<VERSION>.apk`.
6. Merge commits reference the PR number. Releases are tagged `vX.Y.Z` to match
   `VERSION` (`apk-vX.Y.Z` tags are also accepted by the standard).
7. No secrets, tokens, or private keys in commits — ever.

## Build & test

Install:

```bash
pip install cryptography blake3
```

(The server also needs `flask`; the GUI needs a desktop Python with `tkinter`.)

Run the test suite:

```bash
python -m pytest tests/ -v
```

Run the storage server (Flask; stores opaque blobs only — never keys or plaintext):

```bash
cd server
python app.py
```

Run the desktop GUI (tkinter):

```bash
cd gui
python app.py
```

APK builds run in CI (`.github/workflows/main.yml`) on push to `main` and on
`v*` tags. Local APK build prerequisites are documented in `initial_use.md`
(note: it references `server/server.py`; the actual file is `server/app.py`).

## Security

Zerok is a zero-knowledge encrypted container. Read `SECURITY.md`,
`docs/threat-model.md`, and `docs/product-contract.md` before touching crypto
code. The GUI is untrusted with cryptographic operations; violations of the
trust boundaries are security defects.
