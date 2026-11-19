# Changelog

All notable changes to StellarForge CLI will be documented in this file.

The project follows [Semantic Versioning](https://semver.org/) and uses Changesets to capture release intent and generate version/changelog updates.

## 0.1.1

### Patch Changes

- 1a83ca2: Report unknown commands as `unknown command` with a suggestion and the available command list, map subcommand usage errors (such as unknown options) to the validation exit code `2` with a one-line `--help` hint, and redact secret-like values (Stellar secret keys, seed phrases, tokens, passwords) from supervised subprocess output, including values split across output chunks.
- 3dd52af: Harden project generation against a destination being replaced between
  validation and the write phase (SW-127), bound dev-server child output
  forwarding with backpressure so a noisy child can no longer accumulate an
  unbounded output queue (SW-208), and exclude source maps from the
  published package while adding an opt-in `DEBUG=stellarforge` flag for
  diagnostic stack traces (SW-197).
- 3398eae: Return the doctor diagnostic exit code when environment checks fail.
- dde43ac: Restore reliable timeout diagnostics, secret redaction, generator type resolution, and cross-platform CLI validation.
- 9f018c5: Treat blank and whitespace-only dev/start/start:backend values as absent in `stellarforge dev` project discovery; whitespace-only candidates are skipped and a fully blank or missing script set yields actionable guidance with no subprocess spawned. Update the dev supervisor's partial-line buffering so bare carriage returns act as line boundaries for progress frames, keeping 64 KiB bounds and secret redaction per line.
- 7f6d5e0: Add deterministic timeout and termination regression coverage (SW-022). The shared process runner now explicitly sets `killSignal: 'SIGTERM'` to ensure consistent cross-platform termination behavior when a diagnostic command exceeds its timeout. New test coverage in `executable-classification.test.ts` verifies that timed-out processes receive the documented termination sequence, processes that ignore SIGTERM are forcefully stopped within the timeout boundary, no child processes remain after test completion on Linux, macOS, and Windows, and the doctor command completes with a stable timeout failure category. Docker diagnostic tests now verify timeout scenarios are classified distinctly from missing executables or daemon unavailability, with appropriate user-facing remediation messages that do not leak raw error codes.
- 13656ba: Add generator text/binary classification fixtures (SW-034). The new `generator-text-binary-classification.test.ts` suite proves that text files permit only the allowlisted `{{projectName}}` and `{{templateId}}` substitutions, binary files (containing at least one NUL byte) are copied byte-for-byte with no token expansion, classification is purely byte-level and deterministic across repeated calls and independent template trees, and mixed templates process text and binary siblings at every nesting depth without cross-contamination.
- 215eb7a: Add `assertPackageMetadata` and `readPackageJson` to `tests/helpers` (SW-039). The new reusable conformance helper centralises all generated-project package.json assertions — name, scripts, engines.node, absence of dependencies, and absence of embedded secrets — so template tests no longer duplicate that boilerplate. Adopted in `basic-app-package-metadata.test.ts`.
- ec022d8: Preserve readable terminal output at narrow widths and in non-TTY mode (SW-126):
  - Add non-lossy terminal wrapping policy for narrow widths and constrained streams.
  - Ensure file paths, URLs, remediation commands, CLI flags, and Stellar StrKeys remain intact and copyable without ambiguous line splits.
  - Enhance terminal text sanitization to neutralize ANSI CSI cursor movements, OSC window titles, DEC cursor controls, and carriage returns.
  - Accurately measure Unicode display widths (wide CJK, emojis, combining marks) and protect multi-byte sequences and surrogate pairs from corruption.
  - Provide deterministic regression tests and fixtures across supported operating systems.
- 78a33ac: Harden the dev supervisor: reconstruct UTF-8 code points split across child output chunks, finalize child output only after stdout and stderr close (so a sensitive assignment split across exit cannot defeat line-based redaction), and handle stdout/stderr stream errors with a stable, sanitized category while terminating sibling processes. The generated API Service template gains a bounded readiness contract: `GET /health` is a local, bounded liveness probe and `GET /ready` reports initialization completion without exposing internal error details.
- a3c42dc: Clarify Stellar CLI diagnostic remediation for unavailable, malformed, and unsupported versions.
- dc0c5ba: Harden parsing and validation of Stellar CLI deployment output into structured contract data. Accept only canonical Stellar Contract IDs in the official supported output format, reject malformed, truncated, or conflicting IDs, ensure non-zero exits fail safely, and redact secret-like text from output and failure messages.

## 0.1.0 — 2026-09-11

### Added

- Initial public StellarForge CLI MVP foundation and command architecture.
- `stellarforge new` with controlled Basic App, Full Stack, Smart Contract, and API Service templates.
- `stellarforge doctor` diagnostics for Node.js, npm, Git, Rust, Cargo, Stellar CLI, and Docker.
- `stellarforge dev` safe local process orchestration.
- `stellarforge test` unified Node/Cargo test execution.
- `stellarforge deploy` guarded Stellar Testnet smart-contract deployment.
- Versioned `stellarforge.config.json` project configuration with secret-safe validation.
- Cross-platform Linux, macOS, and Windows validation.
- Deterministic linked-CLI end-to-end smoke workflow.
- Changesets-based release management and protected npm/GitHub release automation.
- CodeQL, dependency review configuration, Dependabot, CODEOWNERS, OpenSSF Scorecard, threat modeling, and secure contributor workflows.

### Security

- Uses argument-array subprocess execution with `shell: false`.
- Rejects path traversal, unsafe overwrite targets, raw Stellar StrKeys, secret-like configuration fields, and unsupported deployment networks.
- Keeps signing material in Stellar CLI identity management rather than project configuration.
- Bounds diagnostic subprocess execution time.
- Publishes through npm Trusted Publishing/OIDC once registry bootstrap is completed.

## [Unreleased]

No unreleased user-facing changes yet.
