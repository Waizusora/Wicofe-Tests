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

## Status

Initial repository baseline. Test coverage is not yet comprehensive and no system-wide conformance claim is made. The matrix and strategy are planning artifacts, not passing test evidence.

## License

Original Wicofe test code and documentation are intended to use MPL-2.0. Third-party fixtures retain their own licenses and notices.

## Documentation index

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.
