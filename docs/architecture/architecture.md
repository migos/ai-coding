# Architecture

## Description

Every project uses one of two supported architecture profiles:

- **Simple Architecture**
- **Modular Architecture**

Both profiles follow the same conceptual dependency rules. They differ mainly in how architectural boundaries are physically represented.

Architectural layers are logical boundaries. A logical boundary does not automatically require a Gradle module.

The selected architecture profile affects project structure and dependency enforcement. It does not change the technical quality baseline of the application.

## Rules

- Every project must declare its active architecture profile in `docs/project/decisions.md`.
- Use the smallest architecture that preserves clear ownership and dependency boundaries.
- Do not create Gradle modules merely to reproduce an architecture diagram.
- Do not introduce abstractions solely because they may become useful later.
- Prefer feature-local code until functionality is genuinely shared.
- UI must not directly access database or network implementations.
- Persistence and transport models must not leak into UI.
- Domain models must not depend on Android UI framework classes.
- Do not mix architecture profiles without documenting the exception.
- Project size must not determine whether correctness mechanisms are required.
- Navigation, persistence, lifecycle handling, state restoration, concurrency, and error handling must follow their respective standards regardless of architecture profile.
- Architecture simplification must remove unnecessary structural complexity, not necessary application behavior or correctness guarantees.

## Gotchas

- Do not treat every package as a future Gradle module.
- Do not create a domain layer that only forwards repository calls.
- Do not create use cases for every operation by default.
- Avoid generic `common`, `utils`, or `shared` dumping grounds.
- Do not perform broad architecture migrations during unrelated feature work.
- Fewer modules do not justify ad-hoc navigation, inappropriate persistence, unsafe state handling, or weaker concurrency guarantees.
- Modularization does not automatically create good architecture.
- Simple Architecture is not a reduced-quality version of Modular Architecture.