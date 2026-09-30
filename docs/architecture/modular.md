# Modular Architecture

## Description

Use Gradle modules when isolation, reuse, build performance, or ownership justifies them. The placement tree lists optional modules; individual guidelines define their files.

## Rules

- Feature modules own feature-specific UI and behavior.
- Shared infrastructure belongs in narrowly scoped core modules.
- Feature modules must not depend on other feature implementation modules.
- Shared modules must have a clearly defined purpose.
- Keep public module APIs minimal.
- Avoid circular Gradle dependencies.
- Do not create a module for a single class without a concrete benefit.
- Avoid generic `core:common` or `core:utils` modules.
- Shared data infrastructure must not depend on feature UI.
- Core modules must not depend on feature implementation modules, including their repository interfaces.
- For a repository implemented in `:core:data`, keep its public interface in `:core:data` and its domain-facing models in `:core:model`.
- Keep the implementation internal and assemble it inside the owning module through a DI binding or a narrowly scoped factory.
- A repository owned entirely by a feature may keep its interface and implementation in that feature.
- Do not move it into core solely to follow the example diagram.
- Create only the core modules the project needs.
- Feature UI consumes repository contracts, not constructors of internal implementations.
- Keep `:core:model` independent of feature, database, network, and UI modules.
- Keep database/network modules independent of `:core:data`.
- The app may access infrastructure when assembling the object graph.

## Placement

```text
:app

:feature:<feature-name>

:core:model
:core:data
:core:database
:core:network
:core:ui
```

## Code

```text
:app
  │
  ▼
:feature:<feature-name>
  │
  ├────────► :core:model
  ├────────► :core:data
  └────────► :core:ui

:core:data
  │
  ├────────► :core:model
  ├────────► :core:database
  └────────► :core:network
```

## Gotchas

- Do not create modules solely because a package exists in the Simple profile.
- Too many small modules increase Gradle and maintenance overhead.
- A core module without narrow ownership becomes a dumping ground.
- Feature-to-feature dependencies usually indicate a boundary problem.
- Moving code into a module does not automatically make it reusable.

## References

- [Dependency rules](dependency-rules.md)
- [Repository](../data/repository.md)
- [Dependency injection](../di/dependency-injection.md)
