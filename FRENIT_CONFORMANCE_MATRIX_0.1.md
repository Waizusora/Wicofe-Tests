# Frenit Conformance Matrix 0.1

- **Status:** Planned cross-repository tests; none are claimed as passing by this document.
- **Specification:** [WIC-SPEC-BOOT-FRENIT-001](https://github.com/Waizusora/Wicofe-Spec/blob/main/boot/FRENIT_INTEGRATION_CONTRACT_0.1.md) (Proposed).
- **Implementation:** [Frenit — Free Init](https://github.com/Waizusora/Frenit).
- **Owners:** Frenit, Wicofe-Kernel, Wicofe-ABI, Wicofe-Build, Wicofe-Image, Wicofe-Installer, Wicofe-Test.
- **Date:** 2026-11-18.

## 1. Evidence model

Every executed case records Frenit, kernel, ABI, build and image commit IDs; target architecture; contract/ABI revision; image or fixture digest; execution environment; exact procedure; expected and observed result; and retained logs.

Use `PASS`, `FAIL`, `BLOCKED`, `NOT RUN`, or `NOT APPLICABLE`. Host tests, cross-builds, QEMU runs and physical-hardware runs are separate evidence classes. A planned test or CI job definition is not passing evidence.

## 2. Component-level Frenit tests

| Test ID | Area | Scenario | Minimum expected result |
|---|---|---|---|
| FRN-CLI-001 | CLI parser | Valid domain/action/argument and help/version forms | Canonical `frenit <domain> <action> [argument]` forms parse deterministically |
| FRN-SVC-001 | Service model | Start/stop/restart lifecycle transitions | Invalid transitions are rejected; unimplemented runtime actions are explicit |
| FRN-DEP-001 | Dependency model | Missing dependency, cycle, ordering and conflict | Invalid graphs are rejected; valid ordering is deterministic |
| FRN-CONF-001 | Configuration | Valid, malformed, missing and reload cases | Invalid critical configuration fails safely |
| FRN-REC-001 | Recovery policy | Repeated service crash and retry exhaustion | Retry is bounded and restart storms are prevented |

## 3. Kernel and ABI integration tests

| Test ID | Participants | Scenario | Minimum expected result |
|---|---|---|---|
| FRN-XINT-001 | Wicofe-Kernel, Wicofe-ABI, Frenit | Kernel transfers control to configured init as PID 1 | Entry, initial stack, argv/env, descriptors and credentials match the versioned contract |
| FRN-XINT-002 | Wicofe-Kernel, Wicofe-ABI, Frenit | Spawn/exec, child exit, wait/reap and PID reuse | Exit status is preserved; children are reaped; stale PIDs cannot target replacement processes |
| FRN-XINT-003 | Wicofe-Kernel, Wicofe-ABI, Frenit | Signal delivery and child-exit notification bursts | Required signals are delivered with bounded, documented semantics and no lost waitable child exits |
| FRN-XINT-004 | Wicofe-Kernel, Wicofe-ABI, Frenit | Root filesystem, file descriptors, clock and mount prerequisites | Required operations follow the published ABI and fail explicitly when unavailable |
| FRN-XINT-005 | Frenit, Wicofe-Service | Start/stop/restart a test daemon | Frenit supervises lifecycle while the daemon retains its own service-domain responsibility |
| FRN-XINT-006 | Frenit, Wicofe-Kernel, Wicofe-ABI | Control-plane IPC and peer authorization | Transport and message format match a separately accepted contract; malformed/unauthorized requests fail closed |
| FRN-XINT-007 | Frenit, Wicofe-Kernel, Wicofe-ABI | Graceful shutdown, timeout and power transition | Service stop order and shutdown are bounded; unsupported power operations are never reported as successful |

## 4. Build, image and installation tests

| Test ID | Participants | Scenario | Minimum expected result |
|---|---|---|---|
| FRN-DEPLOY-001 | Wicofe-Build, Frenit | Build Frenit for a declared Wicofe target | Exact source revision, target triple, toolchain, dependency set and artifact hash are recorded |
| FRN-IMAGE-001 | Wicofe-Image, Frenit | Add Frenit to a root-filesystem image manifest | Executable, runtime dependencies, configuration and ABI compatibility metadata are explicit and verified |
| FRN-INSTALL-001 | Wicofe-Installer, Wicofe-Image | Deploy an image containing Frenit | Installed artifacts match the signed/verified manifest; installer does not enable unsupported PID 1 handoff |
| FRN-BOOT-001 | Wicofe-Kernel, Wicofe-Image, Frenit | Boot an installed image | Kernel launches Frenit as PID 1 only after all mandatory ABI and kernel gates pass |

## 5. Fault injection and security

Required negative cases include missing or wrong-architecture Frenit binary; ABI/version mismatch; invalid image manifest; invalid executable path; malformed service configuration; dependency cycles; PID reuse; child-exit and signal bursts; unresponsive services; unauthorized control requests; IPC protocol mismatch; shutdown timeout; and unavailable power-transition mechanisms.

All privileged operations must fail closed. Test cleanup must be idempotent. Do not assume a Unix-domain socket, wire format, syscall number, executable path, or Linux binary ABI until the corresponding Wicofe contract defines it.

## 6. Platform matrix

Run each applicable case independently on x86-64 and AArch64. Host tests validate portable logic only. Cross-builds do not prove native runtime behavior. QEMU and physical-hardware evidence must be reported separately.

## 7. Release gate

No native Frenit PID 1 integration claim is allowed until the kernel handoff, process lifecycle, wait/reap, signal, VFS/descriptor, IPC, shutdown and image deployment gates pass on the claimed architecture. Until then Frenit remains an early-development userspace foundation and must not be installed as production PID 1.
