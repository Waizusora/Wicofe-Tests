# Wicofe Test Strategy

**Status:** Draft baseline

## 1. Purpose

Wicofe-Test coordinates validation across repository boundaries. Unit tests for an individual component should normally remain in that component's repository.

The [Cross-Repository Test Matrix 0.1](CROSS_REPOSITORY_TEST_MATRIX_0.1.md) maps major subsystem contracts to participants and minimum validation. It is a planning document, not evidence that those tests exist or pass.

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
- Fixture/image digest and prerequisites.
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

## 7. ONWFS journal fault-injection matrix

ONWFS journal tests must be tied to the exact journal proposal revision and use disposable disk images. At minimum, the cross-component suite must cover:

- Durable commit followed by loss/torn write of the alternate journal control block; recovery must discover the commit beyond the old persisted head.
- Torn descriptor, metadata image, commit, WRAP record, and control-copy writes.
- Flush/FUA ordering and write-cache capability paths.
- Crash after every home-block write during checkpoint, followed by repeated recovery.
- Ring wrap for every head/tail position and transaction length, including the empty/full sentinel.
- Sequence gaps, stale ring contents, and invalid records after the last valid commit.
- Metadata block free/reallocation ordering: a block freed by an uncheckpointed transaction must remain pending-free and must not be reused until the freeing transaction is checkpointed and tail advancement is persistent.
- Proof that replaying an older redo image cannot overwrite a block already reassigned to file data or a different metadata object.
- Metadata invariants after replay: bitmap ownership, inode/extent references, directory references, link counts, and orphan state.

The test oracle must inspect block ownership and metadata invariants, not only whether mount succeeds. CRC mismatch tests must not be treated as proof against all possible corruption; the fault model and expected error-detection properties must be recorded.

## 8. Requirement traceability

Every cross-component test should reference a stable requirement ID and exact specification revision. Maintain links from requirement to implementation, test case and execution report. Missing implementation or test evidence is a visible gap, not an implicit pass.

See the [Wicofe-Spec traceability policy](https://github.com/Waizusora/Wicofe-Spec/blob/main/governance/SPECIFICATION_TRACEABILITY_POLICY_0.1.md) for the shared requirement metadata and lifecycle model.

## 9. Current status

This strategy and its matrix are plans, not evidence of system-wide test coverage. Each test suite must link to actual CI runs or retained test reports. The matrix must be revised as canonical specifications and implementations evolve.
