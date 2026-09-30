# Simple Architecture

## Description

The Simple Architecture profile is intended for small to medium Android applications that do not require extensive Gradle modularization.

Architectural boundaries are represented primarily through packages inside the application module.

The goal is to keep physical project structure simple while preserving clear ownership, dependency direction, and a migration path toward the Modular Architecture profile.

Simple Architecture simplifies structure, not engineering requirements.

## Rules

- Keep feature-specific code inside the feature package.
- Prefer package boundaries over Gradle modules.
- Do not create separate Gradle modules for individual architectural layers.
- Add a domain package only when it contains meaningful business logic or abstraction.
- Prefer feature-local models until they are genuinely shared.
- UI must not directly access database or network implementations.
- Persistence and transport models must not leak into UI.
- Shared infrastructure may live outside feature packages when multiple features genuinely depend on it.
- Avoid generic `common`, `shared`, or `utils` packages without clear ownership.
- Use the documented navigation solution for independent application destinations.
- Select persistence based on data characteristics and access requirements, not application size.
- Use appropriate state restoration when temporary user state should survive recreation.
- Ensure potentially unbounded UI content remains accessible.
- Data-layer correctness must not depend on UI-level prevention of duplicate actions.
- Concurrency, lifecycle, error handling, persistence, and testing requirements are the same as in the Modular Architecture profile.

## Placement

Recommended package-level structure:

```text
app/
└── src/main/java/<base-package>/
    ├── feature/
    │   └── <feature-name>/
    │       ├── ui/
    │       ├── data/
    │       ├── model/
    │       └── domain/      # only when needed
    ├── navigation/
    ├── database/
    ├── network/
    └── di/
```

A feature should contain only the packages it actually needs.

For example, a feature without domain-specific business logic does not need a `domain/` package.

Code should remain feature-local until moving it creates a concrete benefit such as reuse, ownership, dependency isolation, or clearer responsibility.

## Gotchas

- Do not create empty packages merely for architectural symmetry.
- Do not create abstractions only because they may become useful later.
- Do not move code into shared packages before there is a genuine shared responsibility.
- Do not introduce Gradle modules prematurely.
- Do not interpret `simple` as permission to use ad-hoc navigation or persistence.
- Do not replace established Android solutions with custom state machines solely to reduce code.
- A small application may still require Navigation, Room, DataStore, saved state, transactions, and concurrency control.
- Preserve logical boundaries even when Gradle does not enforce them.