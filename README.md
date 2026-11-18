# Wicofe-Tests

Cross-component test plans, conformance suites, fixtures, and integration validation for Wicofe OS.

## Scope

- Cross-repository integration tests.
- ABI and specification conformance tests.
- Boot and system-level regression scenarios.
- Filesystem, driver, service, and userspace interoperability tests.
- Reusable fixtures and test data that are not owned by a single component.
- Test plans, coverage maps, and reproducibility instructions.

## Ownership boundaries

Unit tests should normally remain beside the code they test. Wicofe-Tests owns tests that validate contracts or behavior across repository boundaries. Component CI remains responsible for fast local checks; this repository coordinates broader integration and conformance coverage.

## Test requirements

- State prerequisites, target architecture, and environment.
- Distinguish automated tests from manual procedures.
- Record expected results and failure criteria.
- Link each conformance test to the relevant specification revision and stable requirement ID.
- Keep fixtures deterministic and avoid embedding secrets or personal data.
- Never report a test as passing unless it was actually executed.

## Core planning documents

- [Test strategy](TEST_STRATEGY.md)
- [Cross-repository test matrix](CROSS_REPOSITORY_TEST_MATRIX_0.1.md)
- [Frenit conformance matrix 0.1](FRENIT_CONFORMANCE_MATRIX_0.1.md) — planned PID 1, process, service, IPC, shutdown and image-handoff tests; no native PID 1 capability is yet verified.

## Status

Initial repository baseline. Test coverage is not yet comprehensive and no system-wide conformance claim is made. The matrix and strategy are planning artifacts, not passing test evidence.

## License

Original Wicofe test code and documentation are intended to use MPL-2.0. Third-party fixtures retain their own licenses and notices.

## Documentation index

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.

## Wicofe-wide principles

Wicofe is a Unix-like, POSIX-compatible, non-GNU alternative. Native components must use Wicofe interfaces and must not require Linux libc or GNU compatibility layers. Prefer permissive licenses; avoid GPL/AGPL, and consider LGPL only when no suitable permissive alternative exists. Reuse or fork upstream software when implementing it from scratch would be disproportionately difficult. See the [canonical project principles](https://github.com/Waizusora/Wicofe-Docs/blob/main/PROJECT_PRINCIPLES.md).
