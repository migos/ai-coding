# Simple Architecture

## Description

The Simple Architecture profile is intended for small to medium Android applications that do not require extensive Gradle modularization.

Architectural boundaries are represented primarily through packages inside the application module.

## Rules

- Keep feature-specific code inside the feature package.
- Prefer package boundaries over Gradle modules.
- Do not create separate Gradle modules for individual layers.
- Add a domain package only when it contains meaningful business logic or abstraction.
- Prefer feature-local models until they are genuinely shared.
- UI must not access DAOs or network clients directly.
- Database entities and network DTOs must not be exposed to UI.
- Shared infrastructure may live outside feature packages when multiple features genuinely use it.
- Avoid generic `common` or `utils` packages.

## Placement

Recommended package-level shape:

```text
app/
└── src/main/java/<base-package>/
    ├── feature/
    │   └── <feature-name>/
    │       ├── ui/
    │       ├── data/
    │       ├── model/
    │       └── domain/      # only when needed
    ├── database/
    ├── network/
    └── di/
```

This tree intentionally shows directories only. Concrete files are defined by the individual implementation guidelines.

## Code

Conceptual feature dependency direction:

```text
ui
 │
 ├──────► domain
 │          │
 │          ▼
 └──────► repository abstraction
               ▲
               │ implemented by
               │
              data
```

## Gotchas

- Do not create empty packages merely for symmetry.
- Do not create repository interfaces just because every feature is expected to have one.
- Do not move code into shared packages before there is an actual shared need.
- Do not introduce Gradle modules prematurely.
- Simple architecture does not remove dependency rules.
