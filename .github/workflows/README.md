# Experimental unsigned build only

This repository contains a manual build wrapper, not an official AltStore release.

Matching public source:
- AltStore PR 1835: https://github.com/altstoreio/AltStore/tree/22f766d2edb47626a082a1f775649bcf3fc43aff
- AltSign: https://github.com/rileytestut/AltSign/tree/832d9cec339ea6ef6891011833dbb7227b028a29
- Each dependency retains its original license.

Only the standard macos-15 runner is allowed. The paid Actions budget stays at $0. The workflow is manual-only, time-limited, unsigned, and accepts no secrets or user inputs. Artifacts are capped at 100 MiB and retained for one day.

No Apple credentials, certificates, private keys, pairing files, private application packages or local project files are supplied to the runner.

A successful build is not proof of successful authentication or device renewal. The upstream repair can select ani.jkcoxson.com or ani2.stikstore.app; provider review and separate consent are required before installing or signing in. Local signing and initialization are still required.
