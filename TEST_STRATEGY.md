# Wicofe Test Strategy

**Status:** Draft baseline

## 1. Purpose

Wicofe-Tests coordinates validation across repository boundaries. Unit tests for an individual component should normally remain in that component's repository.

## 2. Test layers

1. **Static checks:** formatting, linting, metadata validation, dependency/license policy checks.
2. **Unit tests:** component-local behavior; executed by the component repository.
3. **Contract tests:** ABI, file format, module, service, and protocol conformance.
4. **Integration tests:** interactions between two or more repositories.
5. **System tests:** boot, install, update, recovery, and end-to-end workflows in controlled environments.
6. **Hardware tests:** physical-device validation, recorded separately from emulator results.

## 3. Evidence requirements

Every test record must include:

- Test ID and linked requirement/specification revision.
- Source commit(s) under test.
- Target architecture and execution environment.
- Exact command or reproducible procedure.
- Expected and observed result.
- Pass, fail, blocked, or not-run status.
- Logs/artifacts where safe and practical.

A test must never be marked passed unless it was executed successfully. Compilation is not runtime validation; emulator validation is not physical-hardware validation.

## 4. Architecture matrix

Initial target architectures are x86-64 and AArch64. Test coverage must state which architecture was actually exercised. Do not infer AArch64 runtime support from a successful cross-compilation.

## 5. Safety and determinism

Use disposable images and virtual disks for destructive storage tests. Fixtures must be deterministic, versioned, and free of secrets or personal data. Tests must clean up resources and report timeouts distinctly from functional failures.

## 6. Initial priorities

- Kernel boot and memory-management smoke tests.
- ABI layout and syscall contract tests as interfaces are implemented.
- VFS and filesystem conformance tests.
- DKM validation and rejection-path tests before third-party module support.
- Service lifecycle and shutdown integration tests.
- Reproducible image, installer, updater, and recovery tests.

## 7. Current status

This strategy is a plan, not evidence of system-wide test coverage. Each test suite must link to actual CI runs or retained test reports.
