# Modular Architecture

## Description

The Modular Architecture profile uses Gradle modules to enforce meaningful ownership and dependency boundaries.

Modules should exist because they provide a concrete benefit such as isolation, reuse, build performance, or independent ownership.

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

## Placement

Recommended module-level shape:

```text
:app

:feature:<feature-name>

:core:model
:core:data
:core:database
:core:network
:core:ui
```

Not every project needs every core module.

This tree intentionally shows modules only. Concrete files are defined by individual implementation guidelines.

## Code

Typical dependency direction:

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
