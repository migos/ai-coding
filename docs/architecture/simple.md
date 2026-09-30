# Simple Architecture

## Description

Use package boundaries inside `:app` for small to medium applications. Keep ownership and dependency direction explicit; this profile simplifies structure, not correctness requirements.

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
- Create only packages the feature needs.
- Move feature-local code only for a concrete ownership, reuse, or isolation benefit.

## Placement

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

## Gotchas

- Do not create empty packages merely for architectural symmetry.
- Do not create abstractions only because they may become useful later.
- Do not move code into shared packages before there is a genuine shared responsibility.
- Do not introduce Gradle modules prematurely.
- Do not interpret `simple` as permission to use ad-hoc navigation or persistence.
- Do not replace established Android solutions with custom state machines solely to reduce code.
- A small application may still require Navigation, Room, DataStore, saved state, transactions, and concurrency control.
- Preserve logical boundaries even when Gradle does not enforce them.

## References

- [Dependency rules](dependency-rules.md)
- [Navigation](../navigation/navigation.md)
- [Persistence](../data/persistence.md)
