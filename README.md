# Android Agentic Coding Template

Reusable engineering documentation for Android projects developed with coding agents.

## Goal

A guideline should answer:

1. What is this pattern for?
2. What rules apply?
3. Where does it belong?
4. What should the implementation look like?
5. What should be avoided?

## Architecture profiles

The template supports two default profiles.

### Simple

For small to medium applications where package boundaries are sufficient.

Reference:

`docs/architecture/simple.md`

### Modular

For applications where Gradle module boundaries provide a concrete benefit.

Reference:

`docs/architecture/modular.md`

## Default

New projects start with the `simple` profile unless project requirements justify modularization.

The active profile is declared in:

`docs/project/decisions.md`

## Migration

Simple projects can migrate incrementally to the Modular profile.

Reference:

`docs/architecture/migration-simple-to-modular.md`

## Guideline structure

```text
Description
Rules
Placement
Code
Gotchas
```

## Placement rule

If a guideline shows a concrete source file in `Placement`, that same guideline must show the implementation of that file in `Code`.

Do not show unrelated files merely for architectural context. Reference the relevant guideline instead.

## When to extend the documentation

Add or expand a guideline when:

- agents repeatedly implement a pattern inconsistently;
- code reviews repeatedly catch the same issue;
- file placement is ambiguous;
- a framework has project-specific usage rules;
- a recurring implementation can be represented by useful boilerplate;
- a non-obvious edge case repeatedly causes defects.

Do not document every possible Android topic upfront.
