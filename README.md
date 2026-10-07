
# Zerok Container
[![Docs](https://img.shields.io/badge/docs-GitHub%20Pages-blue)](
https://nrupala.github.io/zerok-container/)

Zerok is a zero-trust, zero-knowledge encrypted container for files and blobs.

Your data is encrypted **on your device**, before it is stored anywhere else.
The storage server never sees keys, passwords, or plaintext.

## Core Principles
- Client-side encryption only
- No external GUI frameworks
- No telemetry, no accounts
- Deterministic, auditable behavior
- Beginner-friendly by design

## Who This Is For
- Normal users who want secure storage without learning cryptography
- Developers who want an auditable encrypted core
- Organizations that require zero-knowledge guarantees

## What This Is Not
- A cloud SaaS that reads your data
- A crypto experiment
- A dependency-heavy framework

Zerok is intentionally boring, predictable, and trustworthy.

## Quickstart

```bash
pip install cryptography blake3

# run the storage server (Flask; stores opaque encrypted blobs only)
cd server && python app.py

# run the desktop GUI (tkinter)
cd gui && python app.py
```

See [`initial_use.md`](initial_use.md) for the full getting-started guide and
[`docs/HOW-IT-WORKS.md`](docs/HOW-IT-WORKS.md) for the design.

## Testing

```bash
python -m pytest tests/ -v
```

## Build (Android APK)

The `.github/workflows/main.yml` workflow builds the debug APK on every push to
`main` and on `v*` tags, reading the version from the top-level `VERSION` file
and naming the artifact `zerok-container-<VERSION>.apk`.
