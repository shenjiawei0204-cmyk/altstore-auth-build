# Experimental single-provider build requiring local developer signing

This repository contains a manual build wrapper, not an official AltStore release. It accepts no Apple credentials, keys, pairing files, private application packages or local project data.

## Matching public source and modifications

- AltStore base: https://github.com/altstoreio/AltStore/tree/22f766d2edb47626a082a1f775649bcf3fc43aff
- AltSign: https://github.com/rileytestut/AltSign/tree/832d9cec339ea6ef6891011833dbb7227b028a29
- The workflow embeds the exact reviewed public-source provider, bounded ADI recovery and launch-readiness patch scripts. Each stage checks its pinned input SHA256 before changing sources; Fetch is chained from pristine upstream through the provider intermediate hash to the final recovery hash. BUILD-MANIFEST.json records the effective source hashes and workflow commit; the base commit alone does not describe this modified build.
- The provider is fixed to https://ani.jkcoxson.com. Stored server preferences, public server lists, automatic fallback and the custom provider picker are not used. The settings page shows the fixed address.
- Provider HTTP and WebSocket requests use a dedicated ephemeral session with redirects blocked and credential/cookie storage disabled. Apple provisioning uses its original separate session; TLS validation is not weakened.
- The runner executes network-free Swift origin-policy probes and typechecks the actual fixed-provider settings section against the iOS SDK before building the full app. Each dependency retains its original license, and the output includes the upstream source license and modification notice.
- The migration patch hash-checks DatabaseManager.swift, then stops before migration if the shared container is missing or a database/app source resolves to its own destination. Native Foundation probes cover missing groups, same paths, normalized paths and symbolic links; no migration state is marked complete on those failures.
- The device build disables developer signing. Before packaging, codesign embeds only the pinned public upstream entitlement template using the ad-hoc identity `-`, verifies the nested framework signatures, and independently reads the packaged Mach-O application-group entitlement. This is not an Apple developer signature or provisioning authorization. AltServer must still sign it locally with the independent test account.
- The existing AltWidgetExtension is retained and receives its separately hash-verified, application-group-only template before the main app is processed. Unknown extensions remain blocked. A small real iOS Mach-O ad-hoc signing/readback probe exercises both templates before the full build.

## Cost and access boundary

Only the standard macos-15 runner is allowed. The paid Actions budget stays at $0. The workflow is manual-only, time-limited to 45 minutes, uses a read-only token and accepts no secrets or user inputs. Artifacts are capped at 100 MiB and retained for one day. No Apple developer signing, keychain or provisioning access is enabled; ad-hoc template preparation uses no account, certificate or private key.

## Acceptance boundary

A successful build is not proof of successful authentication or device renewal. The provider receives authentication-helper identity/ADI data during real use; Apple login remains a separate Apple connection. Local signing, installation confirmation and independent-account device validation are still required. Existing applications and backups must remain unchanged during this build-only step.

## Bounded ADI recovery and native gate

Recovery is pre-auth-only: only the original initial fetch opts in, before SRP/OTP work. One shared fresh-preparation budget handles either an incoherent cached tuple or the exact typed helper diagnostic `GetHeadersError` / `ADI Login error: -45061`. There is no generic HTTP503, Apple login/install, later-stage or SIM resync retry. The sole provider remains https://ani.jkcoxson.com with no redirects, fallback or TLS weakening.

Throwing ADI-only storage touches only the three existing helper fields in the nonsynchronizable local keychain, propagates failed reads/removals/writes, verifies invalidation, and publishes identity/blob readbacks before the metadata marker. It does not alter account/password/certificate/pairing storage. Nonblocking auth-lifetime admission starts before initial fetch and lasts through the existing authentication completion; resync uses its independent fetch gate and remains recovery-disallowed. Cancellation is cooperative: an already-running authentication retains admission until its existing callback settles. This is not cross-process keychain atomicity or transport cancellation.

Before the full Xcode build, the runner compiles the generated actual-production-source Foundation probe with `xcrun swiftc -parse-as-library`, runs it, and only after successful execution writes native-result provenance. The scenario count is exported by the exact embedded reviewed core, not a packaging success constant. The probe uses synthetic raw storage faults and no real network/keychain/account requests.

Packaging independently revalidates the exact provider/recovery path sets, pristine/intermediate/final source pins, all five affected final source files, and native-result report/probe/core SHA256 bindings. BUILD-MANIFEST.json contains `adiRecovery` and `adiRecoveryNative` alongside existing provider/launch-readiness evidence. Missing, unsuccessful or stale native evidence stops packaging. The four-file, 100 MiB, one-day artifact envelope is unchanged. A native probe pass and successful iOS build are separate from account authentication, signing, installation and phone-renewal acceptance; `authenticationVerified` and `deviceTested` remain false.
