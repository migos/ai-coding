# Dependency Rules

## Description

These dependency boundaries apply to both architecture profiles. The diagrams show allowed and prohibited access.

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
- In the Modular profile, core modules must not import feature implementation modules to reach repository interfaces or models.
- Keep implementation construction at the composition root or in module-owned bindings/factories.

## Code

### Allowed

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

### Prohibited

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

## References

- [Modular profile](modular.md)
- [Repository](../data/repository.md)
- [Dependency injection](../di/dependency-injection.md)
