# Contributing to Android Engineering Standards

## Guideline Authoring

Engineering guidelines should be small, focused, and directly usable by coding agents.

Each guideline should document one implementation pattern, architectural rule, or closely related group of concerns.

Shared standards must remain generic and reusable across applications.

Examples exist to demonstrate technical patterns, not product-specific use cases.

### Rules

- Use only the sections that add value to the guideline.
- `Description` and `Rules` should normally be present.
- Use `Placement` when the guideline defines where concrete source files belong.
- Use `Code` when the guideline defines a concrete implementation pattern.
- Use `Gotchas` when there are important pitfalls, edge cases, or commonly incorrect implementations.
- Do not add empty or artificial sections only to satisfy a fixed document structure.
- Every concrete source file shown in `Placement` must have a corresponding implementation in `Code`.
- File names shown in `Placement` and `Code` must match exactly.
- Code examples must be complete enough to demonstrate the documented pattern.
- Do not show unrelated files merely to provide architectural context.
- Reference another guideline when surrounding infrastructure is documented elsewhere.
- Keep examples generic and domain-neutral.
- Prefer names such as `Example`, `ExampleScreen`, `ExampleRepository`, and `ExampleEntity`.
- Do not encode assumptions from a specific application into shared standards.
- Generalize lessons from real projects into reusable engineering rules before adding them to shared documentation.
- Avoid duplicating the same rule or boilerplate across multiple guidelines when one document can remain the source of truth.

### Gotchas

- A real project problem is evidence for improving a standard, not a template that should be copied into the standard.
- Large placement trees usually indicate that a guideline is documenting too many responsibilities.
- Product-specific examples can accidentally turn implementation details into perceived standards.
- Duplicated boilerplate creates multiple sources of truth that can drift apart.
- Mandatory sections with no meaningful content add noise and reduce the usefulness of the documentation.