# Contributing to Android Engineering Standards

This file defines how to maintain the standards. Application guidelines belong in `docs/`; authoring guidance stays here.

## Rules

- Keep each guideline focused and reusable across applications.
- Include `Description` and `Rules` when useful; omit empty sections.
- Use short bullet points in `Rules` and `Gotchas`.
- Keep `Description` concise; include only context needed to apply the pattern.
- Add optional `Selection` for decision tables when several mechanisms are valid.
- Use `Placement` only for concrete file, package, or module locations.
- Put only paths/trees and profile headings in `Placement`; no explanatory prose.
- Every concrete source/resource file in `Placement` must have a matching example in `Code`.
- Put only fenced examples and identifying headings in `Code`; no explanatory prose.
- Identify complete references versus illustrative fragments in `Description` or `Rules`.
- Include packages, imports, and required resources in complete source references.
- Put supporting documentation links in optional `References` as a bullet list.
- Use generic examples and keep shared types consistent across guidelines.
- Keep one source of truth per rule; reference related guidelines instead of duplicating them.
- Preserve dependency direction across profiles and explain necessary package/import adaptations in `Rules`.
- Record product-specific choices in project decisions or scoped exceptions.
- Update the index and reading matrix in `docs/README.md` when guidelines change.
- Keep this repository documentation-only; do not add validation scripts or generated projects.

## Gotchas

- Long explanations and duplicate examples reduce the usefulness of AI instructions.
- Formatting changes must preserve correctness rules and example behavior.
- Check local links, placement/example names, and profile consistency after documentation changes.
- Compile changed source examples and test their behavior in a suitable external Android environment.
- Report checks that ran and any validation limitations.

## References

- [Project setup](docs/project/setup.md)
- [Agent instructions](AGENTS.md)
- [Engineering standards index](docs/README.md)
