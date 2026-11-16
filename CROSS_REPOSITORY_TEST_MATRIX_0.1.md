# Wicofe Cross-Repository Test Matrix 0.1

**Status:** Draft planning baseline  
**Owner:** Wicofe-Tests  
**Scope:** Cross-component contracts and system behavior. Component-local unit tests remain in their owning repositories.

## 1. Purpose and evidence rules

This matrix maps major Wicofe integration domains to their canonical contract owners, producers/consumers, required test classes and minimum acceptance evidence. It is a planning baseline, not a report of existing test coverage.

Every test case must have a stable test ID and link to:
- requirement/specification ID and exact revision;
- source commit(s) for every participating repository;
- architecture and CPU baseline;
- execution environment (host, emulator, or physical hardware);
- fixture/image digest and setup prerequisites;
- exact command or reproducible procedure;
- expected result and failure criteria;
- observed result, status and retained logs/artifacts.

Allowed execution statuses: **PASS**, **FAIL**, **BLOCKED**, **NOT RUN**, **NOT APPLICABLE**. A test may be marked PASS only after execution and inspection of its result. Keep compile, link, static validation, emulator execution and physical hardware execution as distinct evidence levels.

## 2. Cross-repository coverage matrix

| Domain | Canonical authority | Main participants | Minimum cross-component validation |
|---|---|---|---|
| Boot handoff | Wicofe-Kernel boot specification; Limine external protocol | Wicofe-Build, Wicofe-Image, Wicofe-Installer, Wicofe-Tests | Image structure, protocol response validation, malformed handoff rejection, memory-map bounds, x86-64 and AArch64 boot milestones |
| Userspace syscall ABI | Wicofe-ABI | Wicofe-Kernel, Wicofe-Libc, Wicofe-Lib, Wicofe-SDK | Layout/offset checks, raw entry/return, pointer validation, error encoding, version compatibility |
| DKM | Wicofe-Kernel | Wicofe-Drivers, Wicofe-Build, Wicofe-ThirdParty | Metadata validation, architecture/ABI mismatch, dependency and relocation failures, init rollback, signature rejection, unsafe-unload refusal |
| VFS and filesystems | Wicofe-Kernel for VFS; ONWFS for ONWFS format | ONWFS, Wicofe-Storage, Wicofe-ABI, Wicofe-Libc | Mount lifecycle, path/descriptor semantics, malformed images, feature negotiation, metadata invariants, recovery behavior |
| ONWFS journal | ONWFS journal proposal | Wicofe-Kernel, Wicofe-Storage, Wicofe-Recovery | Torn writes, commit/control divergence, replay idempotence, ring wrap, deferred-free protection, flush/FUA failure |
| Drivers and device I/O | Wicofe-Kernel driver core; Wicofe-Drivers device implementations | Wicofe-ABI, Wicofe-Services, Wicofe-Tests | Probe/bind, resource conflicts, DMA bounds, interrupt lifecycle, device removal and failure recovery |
| Storage administration | Wicofe-Storage | Wicofe-Kernel, ONWFS, Wicofe-Recovery | Stable device identity, stale plan rejection, partition boundary validation, destructive-operation interruption and post-write verification |
| Networking | Wicofe-Network | Wicofe-Kernel, Wicofe-Drivers, Wicofe-Services, Wicofe-ABI | Interface lifecycle, packet boundary validation, configuration/route/DNS behavior, privilege enforcement, link loss and recovery |
| Input | Wicofe-Input | Wicofe-Kernel, Wicofe-Drivers, WDE | Device hotplug, event ordering, seat/session isolation, malformed event rejection and permission boundaries |
| Graphics | Wicofe-Graphics | Wicofe-Kernel, Wicofe-Drivers, WDE | Display discovery, modeset failure, buffer lifetime/synchronization, GPU reset and compositor recovery |
| Audio | Wicofe-Audio | Wicofe-Kernel, Wicofe-Drivers, Wicofe-Services | Stream lifecycle, format negotiation, clock/latency behavior, hotplug, USB DAC bit-perfect evidence where claimed |
| Services and init | Osynit owns PID 1/service management; Wicofe-Services owns daemons | Wicofe-Kernel, Wicofe-ABI, Wicofe-Lib, Wicofe-Tests | Dependency ordering, readiness, crash/restart policy, shutdown, privilege separation and IPC failure |
| Rust/C userspace runtime | Wicofe-Libc and Wicofe-Lib; Wicofe-ABI owns syscall contract | Wicofe-SDK, Wicofe-Kernel, component consumers | Startup, allocator, I/O, process/thread primitives, TLS and runtime execution on target |
| SDK and ports | Wicofe-SDK owns toolchain; Wicofe-Ports owns recipes; Wicofe-ThirdParty owns compliance records | Wicofe-Build, WPM, consuming components | Host contamination, pinned toolchain, source digest, reproducible build, target ABI validation and runtime level |
| Image and installation | Wicofe-Image owns image composition; Wicofe-Installer owns installation | Wicofe-Build, Wicofe-Kernel, Wicofe-Storage | Manifest/artifact verification, target identity revalidation, install interruption, bootable result and data-preservation checks |
| Whole-system update | Wicofe-Update | WPM, Wicofe-Image, Wicofe-Recovery, Osynit | Trust/anti-rollback, compatibility, concurrent package lock, activation interruption, health confirmation and rollback/data migration |
| Recovery and restore | Wicofe-Recovery orchestrates; format/update owners retain canonical recovery semantics | ONWFS, Wicofe-Storage, Wicofe-Update, WPM | Isolated recovery boot, read-only default, destructive consent, backup verification, restore interruption and updater handoff |

The table names contract owners; it does not imply that any participant or feature is implemented.

See the [Osynit Core Init conformance matrix 0.1](OSYNIT_CONFORMANCE_MATRIX_0.1.md) for the detailed mandatory-core, kernel/ABI, IPC, daemon, image and first-boot cases.

## 3. Test design requirements

### 3.1 Contract tests

Contract tests validate externally observable boundaries rather than private implementation details. Prefer byte-level fixtures, generated ABI layout assertions, protocol state-machine tests and explicit error behavior.

A contract test must identify which specification revision it implements. When a specification changes incompatibly, mark affected tests for review rather than silently updating expected values.

### 3.2 Integration tests

An integration test must include at least two independently versioned repositories or components. Record all participating commits. If a dependency is represented by a mock, label the test as mocked and do not claim end-to-end validation.

### 3.3 System tests

System tests run a composed image or booted environment. Record bootloader, firmware mode, image digest, emulator/hardware model, memory/CPU configuration, architecture and serial/diagnostic output.

### 3.4 Fault-injection tests

For storage, update, installer, recovery and lifecycle operations, inject failure at each durable state transition where feasible. Verify both on-disk state and user-visible result. Test recovery more than once to establish idempotence.

### 3.5 Security tests

Test malformed and adversarial inputs at trust boundaries: boot metadata, syscall pointers, module metadata, filesystem images, device identifiers, package manifests, archives, network messages and privileged IPC. Record resource limits and expected fail-closed behavior.

## 4. Architecture and execution matrix

Initial architecture targets are x86-64 and AArch64. Each test declares one of:
- **Host-only:** validates build tooling or host-side logic.
- **Cross-build:** produces target artifacts but does not execute them.
- **Static target validation:** inspects target artifact metadata, symbols, layout or dependencies.
- **Emulator runtime:** executes on a named emulator and machine profile.
- **Physical hardware:** executes on a named hardware revision and firmware configuration.

Do not promote evidence from one category to another. A successful AArch64 cross-build is not AArch64 boot evidence.

## 5. Test fixture policy

- Use deterministic, versioned fixtures with published hashes.
- Keep golden binary images tied to the exact format/specification version.
- Use disposable disk images for destructive operations.
- Do not include credentials, keys, personal data or production disk images.
- Record fixture generator version and seed for randomized tests.
- Preserve minimized regression inputs for fuzzing failures.
- Make cleanup idempotent and report leaked resources.

## 6. Requirement traceability

Each normative requirement should have a stable identifier in its owning specification. Test IDs should refer to those requirement IDs. Maintain links in both directions:
- requirement → implementation location → test case(s) → latest execution evidence;
- test case → requirement revision → participating source commits.

A missing implementation or test link is a visible coverage gap, not an implicit pass. See Wicofe-Specs' specification traceability policy for the shared requirement metadata model.

## 7. Initial execution priorities

1. Boot handoff and kernel smoke tests for the currently supported x86-64 path.
2. ABI layout and syscall boundary tests as interfaces become executable.
3. VFS/filesystem parser and ONWFS golden-image/fault-injection tests.
4. DKM rejection-path tests before enabling third-party module loading.
5. Driver lifecycle and service/init integration tests.
6. Reproducible build/image and installer safety tests.
7. Update/recovery interruption and restore tests.
8. Networking, input, graphics and audio cross-component runtime tests.
9. AArch64 emulator/hardware milestones as target bring-up becomes available.

Priorities describe recommended order, not current pass status.

## 8. Reporting and release gates

A release candidate must publish:
- test matrix revision;
- exact source commits and build inputs;
- architecture-specific execution summary;
- PASS/FAIL/BLOCKED/NOT RUN counts;
- known coverage gaps and waived tests with rationale;
- logs and artifacts, excluding secrets;
- unresolved failures and risk acceptance owner.

No system-wide conformance claim is permitted while required contract tests are missing, blocked or not run. A test report must never be replaced by a badge without retained evidence.

## 9. Current status

This document and the repository test strategy are planning artifacts. They do not demonstrate that the listed tests, CI jobs, emulators, hardware runs or subsystem implementations currently exist.
