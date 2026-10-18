# Experimental unsigned, single-provider build

This repository contains a manual build wrapper, not an official AltStore release. It accepts no Apple credentials, keys, pairing files, private application packages or local project data.

## Matching public source and modifications

- AltStore base: https://github.com/altstoreio/AltStore/tree/22f766d2edb47626a082a1f775649bcf3fc43aff
- AltSign: https://github.com/rileytestut/AltSign/tree/832d9cec339ea6ef6891011833dbb7227b028a29
- The workflow embeds the exact public-source patch script. It checks the original SHA256 of three files before modifying them. BUILD-MANIFEST.json records the effective source hashes and workflow commit; the base commit alone does not describe this modified build.
- The provider is fixed to https://ani.jkcoxson.com. Stored server preferences, public server lists, automatic fallback and the custom provider picker are not used. The settings page shows the fixed address.
- Provider HTTP and WebSocket requests use a dedicated ephemeral session with redirects blocked and credential/cookie storage disabled. Apple provisioning uses its original separate session; TLS validation is not weakened.
- The runner executes network-free Swift origin-policy probes before building the iOS app. Each dependency retains its original license, and the output includes the upstream source license and modification notice.

## Cost and access boundary

Only the standard macos-15 runner is allowed. The paid Actions budget stays at $0. The workflow is manual-only, time-limited to 45 minutes, unsigned, uses a read-only token and accepts no secrets or user inputs. Artifacts are capped at 100 MiB and retained for one day. No signing or provisioning access is enabled.

## Acceptance boundary

A successful build is not proof of successful authentication or device renewal. The provider receives authentication-helper identity/ADI data during real use; Apple login remains a separate Apple connection. Local signing, installation confirmation and independent-account device validation are still required. Existing applications and backups must remain unchanged during this build-only step.
