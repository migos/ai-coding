# Android Agentic Coding Template

Reusable documentation for AI-assisted Kotlin/Compose Android development.

## Scope

- Start with `simple`; use `modular` when Gradle boundaries provide a concrete benefit.
- Apply the same correctness baseline to both profiles.
- Use Navigation 3 for multiple independent Compose destinations by default.
- Record active choices in project decisions before generating application code.
- Keep this repository documentation-only.

## Guideline structure

- `Description`: concise context and purpose.
- `Rules`: short, actionable bullet points.
- `Selection`: optional decision table.
- `Placement`: optional locations and profile headings, without prose.
- `Code`: optional fenced examples and identifying headings, without prose.
- `Gotchas`: short bullet points for meaningful pitfalls.
- `References`: optional links to related and official documentation.

## When to extend

- Add guidance for recurring defects, ambiguous placement, or inconsistent implementations.
- Generalize lessons instead of copying product-specific requirements.
- Avoid documenting every possible Android topic upfront.

## References

- [Agent instructions](AGENTS.md)
- [Engineering standards index](docs/README.md)
- [Authoring rules](CONTRIBUTING.md)
- [Project setup](docs/project/setup.md)
- [Project decisions](docs/project/decisions.md)
- [Simple profile](docs/architecture/simple.md)
- [Modular profile](docs/architecture/modular.md)
- [Incremental migration](docs/architecture/migration-simple-to-modular.md)
