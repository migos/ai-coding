# Migration: Simple to Modular

## Description

Migrate incrementally by extracting stable boundaries while preserving behavior. Strengthen dependency enforcement without redesigning unrelated code.

## Rules

- Do not migrate solely because the project has grown in file count.
- Preserve behavior during migration.
- Move one clear boundary at a time.
- Keep dependency direction valid at every step.
- Do not combine broad refactoring with unrelated feature work.
- Prefer moving stable boundaries before introducing new abstractions.
- Update the active architecture profile only when the migration is actually complete enough to follow the Modular rules.
- Identify a stable boundary and extract its dependencies first.
- Move code without changing behavior.
- Compile and test after each extraction.

## Gotchas

- Do not modularize unstable boundaries first.
- Do not move every model into `:core:model`.
- Do not turn `:core:data` into a dumping ground.
- Migration should reduce coupling, not merely increase file movement.

## References

- [Simple profile](simple.md)
- [Modular profile](modular.md)
- [Dependency rules](dependency-rules.md)
