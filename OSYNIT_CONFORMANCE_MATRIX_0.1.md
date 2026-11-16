# Osynit Core Init Conformance Matrix 0.1

- **Status:** Planned cross-repository tests; none are claimed as passing by this document
- **Specification:** [Osynit Core Architecture Contract 0.1](https://github.com/Waizusora/Wicofe-Specs/blob/main/boot/OSYNIT_CORE_ARCHITECTURE_CONTRACT_0.1.md)
- **Implementation:** [Osynit](https://github.com/Waizusora/Osynit)
- **Date:** 2026-11-16

## 1. Evidence model

Every case records Osynit, kernel, ABI, build, and image commit IDs; target architecture; ABI/capability revision; test fixture/image digest; execution environment; command/procedure; expected result; observed result; and retained logs.

Use the repository's standard states: PASS, FAIL, BLOCKED, NOT RUN, NOT APPLICABLE. Host tests, cross-builds, emulator runs, and physical hardware runs are separate evidence classes.

## 2. Core Init tests

| Test ID | Core responsibility | Required scenario | Minimum expected result |
|---|---|---|---|
| OSY-KIF-001 | Kernel Interface | Probe required and optional platform capabilities | Required primitives are identified; absent optional facilities are reported explicitly |
| OSY-PROC-001 | Process Supervisor | Spawn, track, exit, wait and reap multiple children | Every waitable child is reaped; no zombie remains |
| OSY-PROC-002 | Process Supervisor | Orphan adoption and rapid PID reuse | Adoption follows kernel contract; stale PID cannot target a replacement process |
| OSY-SVC-001 | Service Manager | Start/stop/restart oneshot and foreground services | Lifecycle states and exit results are deterministic |
| OSY-DEP-001 | Dependency Manager | Missing dependency, cycle, ordering and conflict cases | Invalid graph is rejected; valid order is deterministic |
| OSY-BOOT-001 | Boot Manager | Execute default target with required and best-effort units | Boot result and critical failure are reported correctly |
| OSY-STATE-001 | System State Manager | Exercise every allowed and forbidden state transition | Allowed transitions succeed; invalid transitions are rejected |
| OSY-SIG-001 | Signal Manager | SIGCHLD bursts and shutdown-related signal delivery | No lost child exits; event handling remains bounded |
| OSY-SHUT-001 | Shutdown Manager | Reverse dependency stop order, graceful timeout and forced termination | New jobs are rejected; shutdown is bounded and follows policy |
| OSY-CONF-001 | Configuration Loader | Valid, malformed, missing and reload-during-operation configurations | Invalid critical configuration fails safely; reload is atomic or explicitly rejected |
| OSY-RUN-001 | Runtime Manager | Runtime directory creation, stale socket/lock handling and cleanup | Ownership/modes are restrictive; live manager resources are not removed |
| OSY-REC-001 | Failure Recovery Manager | Repeated crash, restart backoff and rate-limit exhaustion | Retry policy is bounded; restart storm is prevented |
| OSY-IPC-001 | IPC / Control Interface | Wrong protocol version, malformed frame, oversized request and unauthorized peer | Requests fail closed with stable error codes |
| OSY-IPC-002 | IPC / Control Interface | Concurrent clients during service transitions | State changes are serialized or transactionally consistent; no partial success is reported |

## 3. Cross-repository integration tests

| Test ID | Participants | Scenario | Minimum expected result |
|---|---|---|---|
| OSY-XINT-001 | Wicofe-Kernel, Wicofe-ABI, Osynit | Kernel launches configured init as PID 1 | Correct argv/env/fd/credentials contract; Osynit enters init mode |
| OSY-XINT-002 | Wicofe-Kernel, Wicofe-ABI, Osynit | Child exit, wait/reap and orphan adoption | Kernel semantics match ABI and Osynit process supervisor |
| OSY-XINT-003 | Wicofe-ABI, Osynit | Unix-domain IPC and peer credentials | Versioned bounded protocol and operation authorization work |
| OSY-XINT-004 | Wicofe-Services, Osynit | Start/stop/restart external daemon | Osynit supervises lifecycle; daemon retains domain ownership |
| OSY-XINT-005 | Wicofe-Build, Wicofe-Image, Osynit | Target artifact handoff | Correct architecture, hash, ABI metadata and executable path are staged |
| OSY-XINT-006 | Wicofe-Image, Wicofe-Installer, Wicofe-Kernel, Osynit | Boot installed image | Kernel launches Osynit as PID 1 and selected default target is reached |
| OSY-XINT-007 | Osynit, Wicofe-Services | Failure recovery and system shutdown | External services stop in reverse dependency order with bounded timeouts |

## 4. Fault injection and security

Required negative tests include:
- child-exit storms, signal storms, PID reuse, and interrupted wait operations;
- malformed unit/configuration files, path traversal, unsafe executable paths, and implicit-shell injection attempts;
- dependency cycles, missing required units, and conflicting target transactions;
- repeated service crashes and restart-limit exhaustion;
- shutdown timeout, unresponsive process, and unavailable power-transition primitive;
- stale IPC socket, wrong peer credentials, oversized messages, invalid protocol version, and request flooding;
- missing/incorrect init binary, architecture mismatch, ABI mismatch, and invalid image manifest.

All privileged IPC and destructive lifecycle operations must fail closed. Test cleanup must be idempotent.

## 5. Platform matrix

Run each applicable case independently on x86-64 and AArch64. A host test validates portable logic only. A successful cross-build does not establish runtime behavior. Emulator boot evidence and physical hardware evidence must be reported separately.

## 6. Release gate

No native Osynit PID 1 conformance claim is allowed until the required kernel/ABI, process, signal, IPC, service, shutdown, and image-handoff tests pass on the claimed architecture. A documented test case or CI job definition is not a passing result.
