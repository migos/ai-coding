# Dependency Rules

## Description

Dependency rules define which architectural components may depend on each other.

They apply to both architecture profiles.

## Rules

- UI may depend on domain/application behavior and repository abstractions.
- UI must not depend directly on DAOs, Room entities, DTOs, HTTP clients, or database implementations.
- Repository implementations may depend on local and remote data sources.
- Repository APIs expose domain-facing models.
- Database entities remain inside the data/database boundary.
- Network DTOs remain inside the data/network boundary.
- Mapping occurs at boundaries.
- Domain models must not depend on UI objects.
- Dependency injection must not be used to bypass invalid dependency direction.
- Avoid service-locator access from arbitrary classes.

## Placement

This guideline defines dependency behavior and does not create source files.

## Code

Allowed:

```text
UI
 │
 ▼
Repository abstraction
 ▲
 │
Repository implementation
 │
 ├──► DAO
 └──► Remote data source
```

Not allowed:

```text
ViewModel ──► DAO
ViewModel ──► HTTP Client
Composable ──► Repository Implementation
Domain Model ──► Android View
```

## Gotchas

- DI does not make an invalid dependency valid.
- A DTO or entity with identical fields to a domain model is still a boundary-specific model.
- Convenience imports across boundaries often become permanent coupling.
