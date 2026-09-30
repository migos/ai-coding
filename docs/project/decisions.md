# Project Decisions

This file contains active decisions. Copyable authoring guidance belongs in Project Setup, not among active entries.

## Architecture Profile

Decision: `simple`

Allowed values: `simple`, `modular`.

Rationale: Start with package boundaries and add Gradle boundaries only when they provide a concrete benefit.

## Language and UI

Decision: Kotlin and Jetpack Compose.

Rationale: These shared reference patterns target Kotlin/Compose Android applications. Other UI stacks require an explicit project decision and applicable replacement guidance.

## Navigation

Decision: Navigation 3 for multiple independent Compose destinations.

Rationale: Use typed, saveable back-stack keys and entry-owned state according to Navigation.

## Template adoption status

This repository contains engineering documentation, not an initialized Android application. Toolchain versions, SDK levels, DI choice, build variants, and verification commands must be recorded when adopting it; follow Project Setup. Do not treat this paragraph as completed application setup.

## References

- [Project Setup](setup.md)
- [Navigation](../navigation/navigation.md)
- [Project Setup](setup.md)
