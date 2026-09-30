# Architecture

## Description

Every project uses one of two supported architecture profiles:

- **Simple Architecture**
- **Modular Architecture**

Both profiles follow the same conceptual dependency rules. They differ mainly in how boundaries are physically represented.

Architectural layers are logical boundaries. A logical boundary does not automatically require a Gradle module.

## Rules

- Every project must declare its active architecture profile in `docs/project/decisions.md`.
- Use the smallest architecture that preserves clear ownership and dependency boundaries.
- Do not create Gradle modules merely to reproduce an architecture diagram.
- Do not introduce abstractions solely because they may become useful later.
- Prefer feature-local code until functionality is genuinely shared.
- UI must not directly access database or network implementations.
- Persistence and transport models must not leak into UI.
- Domain models must not depend on Android UI framework classes.
- Do not mix profiles without documenting the exception.

## Placement

Architecture profile documentation:

```text
docs/architecture/
├── simple.md
├── modular.md
└── migration-simple-to-modular.md
```

These are documentation files, not Android source files.

## Code

Conceptual dependency direction:

```text
UI
 │
 ▼
Domain / Application Logic
 │
 ▼
Data Abstractions
 │
 ▼
Data Implementations
 │
 ├── Database
 └── Network
```

Not every feature requires every layer.

## Gotchas

- Do not treat every package as a future Gradle module.
- Do not create a domain layer that only forwards repository calls.
- Do not create use cases for every operation by default.
- Avoid generic `common`, `utils`, or `shared` dumping grounds.
- Do not perform broad architecture migration during unrelated feature work.
