# Guideline Format

## Description

Engineering guidelines should be small, focused, and directly usable by coding agents.

Each guideline teaches one implementation pattern or one closely related group of files.

## Rules

- Use the sections `Description`, `Rules`, `Placement`, `Code`, and `Gotchas`.
- `Placement` is required when the guideline creates concrete source files.
- Every concrete source file shown in `Placement` must appear in `Code`.
- File names in `Placement` and `Code` must match exactly.
- Code must be complete enough to serve as boilerplate.
- Do not show unrelated files in a placement tree.
- Reference other guidelines for surrounding infrastructure.
- Use generic names such as `Example`, `ExampleRepository`, and `ExampleScreen`.
- Avoid domain-specific examples in shared documentation.

## Placement

This guideline defines documentation structure and does not create Android source files.

## Code

Recommended guideline skeleton:

```md
# Pattern Name

## Description

Short explanation of purpose.

## Rules

- Mandatory rule.
- Mandatory rule.

## Placement

Show only the concrete files taught by this guideline.

## Code

Provide one code block for every concrete file shown in Placement.

## Gotchas

- Known issue.
- Important exception.
```

## Gotchas

- Large placement trees usually indicate the guideline is teaching too many things.
- Duplicating the same boilerplate across multiple guidelines creates conflicting sources of truth.
- Context-only source files should be referenced, not shown.
