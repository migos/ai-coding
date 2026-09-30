# Project Decisions

## Description

This file records project-specific technical decisions that affect future implementation.

The template defaults to the Simple Architecture profile.

## Rules

- Record only decisions that materially affect future implementation.
- Keep entries concise.
- Update superseded decisions instead of keeping contradictory active rules.
- Do not use this file as a changelog.
- Change the architecture profile only intentionally.

## Placement

```text
docs/project/
└── decisions.md
```

This is a documentation file and does not create Android source code.

## Code

Default architecture decision:

```md
## Architecture Profile

Decision:

`simple`

Allowed values:

- `simple`
- `modular`

Rationale:

Use the smallest architecture that preserves clear dependency and ownership boundaries.
```

Template for additional decisions:

```md
## <Decision Name>

Decision:

<decision>

Rationale:

<short reason>

Affected areas:

- <area>
```

## Gotchas

- A preference is not automatically a project decision.
- Temporary implementation details usually do not belong here.
- Changing the architecture profile changes placement expectations across the project.
