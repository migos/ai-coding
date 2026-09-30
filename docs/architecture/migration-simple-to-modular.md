# Migration: Simple to Modular

## Description

Migration from the Simple Architecture profile to the Modular Architecture profile must be incremental.

The goal is to strengthen existing boundaries, not redesign the application unnecessarily.

## Rules

- Do not migrate solely because the project has grown in file count.
- Preserve behavior during migration.
- Move one clear boundary at a time.
- Keep dependency direction valid at every step.
- Do not combine broad refactoring with unrelated feature work.
- Prefer moving stable boundaries before introducing new abstractions.
- Update the active architecture profile only when the migration is actually complete enough to follow the Modular rules.

## Placement

Example progression:

```text
Step 0
:app

Step 1
:app
:core:model

Step 2
:app
:core:model
:core:database

Step 3
:app
:core:model
:core:database
:core:data

Step 4
:app
:feature:<feature-name>
:core:model
:core:database
:core:data
```

These are module boundaries only. Concrete file moves must follow the relevant implementation guidelines.

## Code

Typical migration sequence:

```text
1. Identify an already-stable package boundary.
2. Extract its shared dependencies first.
3. Create the target Gradle module.
4. Move the stable code without redesigning behavior.
5. Fix dependencies so they follow the documented direction.
6. Compile and test.
7. Repeat for the next boundary only when useful.
```

## Gotchas

- Do not modularize unstable boundaries first.
- Do not move every model into `:core:model`.
- Do not turn `:core:data` into a dumping ground.
- Migration should reduce coupling, not merely increase file movement.
