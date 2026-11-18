# Test Documentation Index

This directory documents cross-repository validation, conformance suites, integration tests, and reusable fixtures.

## Core planning documents
- [Test strategy](../TEST_STRATEGY.md) — test layers, evidence requirements, architecture coverage and ONWFS journal fault-injection priorities.
- [Cross-repository test matrix 0.1](../CROSS_REPOSITORY_TEST_MATRIX_0.1.md) — subsystem ownership, integration participants and minimum conformance coverage.
- [Frenit conformance matrix 0.1](../FRENIT_CONFORMANCE_MATRIX_0.1.md) — native init, process, signal, service, image and shutdown integration gates.

## Scope
- ABI and specification conformance.
- Cross-repository integration and interoperability.
- Boot, system, filesystem, driver, service, and userspace end-to-end tests.
- Shared fixtures, test plans, coverage maps, and reproducibility instructions.

## Ownership
Unit tests normally remain beside the implementation they test. Wicofe-Tests owns tests that validate behavior across repository boundaries. Component CI remains responsible for fast local checks.

## Evidence requirements
Every test identifies specification revision, prerequisites, architecture, environment, procedure, expected result, and failure criteria. Distinguish compile checks, static analysis, emulator execution, and physical-hardware validation. Never report a test as passing unless it was run.

- [Wicofe-Specs traceability policy](https://github.com/Waizusora/Wicofe-Specs/blob/main/governance/SPECIFICATION_TRACEABILITY_POLICY_0.1.md)
- [Central Wicofe documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
