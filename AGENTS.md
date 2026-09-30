# Engineering Instructions

This repository follows documented Android engineering standards intended for both humans and coding agents.

The documentation defines:

- Architecture
- Dependency direction
- Navigation
- Persistence
- State ownership
- File placement
- Reference implementations
- Known pitfalls
- Project-specific decisions and exceptions

## Mandatory first step

Before implementing, moving, or generating code:

1. Read `docs/project/decisions.md`.
2. Determine the active architecture profile:
   - `simple`
   - `modular`
3. Read the corresponding architecture profile:
   - `docs/architecture/simple.md`
   - `docs/architecture/modular.md`
4. Read the guideline files relevant to the requested change.
5. Inspect existing implementations of the same or a similar pattern.

Do not invent a third architecture profile unless explicitly requested.

## Instruction hierarchy

When instructions conflict, use this order:

1. Existing project-specific code and documented project decisions
2. `docs/project/exceptions.md`
3. Active architecture profile
4. Shared engineering guidelines in `docs/`
5. General Android and Kotlin conventions

The more specific rule wins.

## Quality baseline

The active architecture profile controls physical project structure. It does not reduce the technical quality baseline.

Choose the simplest implementation that satisfies the documented requirements for:

- Architecture
- Navigation
- Persistence
- Lifecycle handling
- State restoration
- Concurrency
- Error handling
- Testing

Do not choose an implementation solely because it requires less code.

Application size must not be used as justification for ad-hoc navigation, inappropriate persistence, unsafe state handling, or incorrect concurrency behavior.

## Implementation workflow

Before writing code:

1. Confirm the active architecture profile.
2. Inspect existing implementations.
3. Read the relevant guidelines.
4. Confirm file placement.
5. Reuse existing abstractions where appropriate.
6. Prefer established project patterns over introducing new ones.
7. Select APIs and technologies according to the documented requirements rather than implementation convenience.

During implementation:

1. Keep changes scoped to the requested task.
2. Do not refactor unrelated code.
3. Follow dependency boundaries.
4. Keep public APIs as small as possible.
5. Add or update tests when behavior changes.
6. Avoid adding libraries unless necessary.
7. Do not introduce architectural layers or Gradle modules without a concrete reason.
8. Do not replace established platform solutions with ad-hoc state, navigation, or persistence mechanisms.
9. Do not rely on UI behavior to guarantee data-layer correctness.

After implementation:

1. Verify affected modules compile.
2. Run relevant tests.
3. Run formatting and static analysis if configured.
4. Verify file placement against the active architecture profile.
5. Verify documented dependency rules.
6. Verify navigation behavior when destinations were added or changed.
7. Verify persistence choices when durable data was introduced or changed.
8. Verify required state restoration behavior.
9. Verify potentially unbounded UI content remains accessible.
10. Verify exposed failures have an intentional presentation or handling path.
11. Verify data mutations remain correct when multiple operations overlap.
12. Report intentional deviations.

## Documentation map

```text
docs/
├── architecture/
│   ├── architecture.md
│   ├── simple.md
│   ├── modular.md
│   ├── dependency-rules.md
│   └── migration-simple-to-modular.md
│
├── navigation/
│   └── navigation.md
│
├── ui/
│   ├── screen.md
│   ├── viewmodel.md
│   └── ui-state.md
│
├── data/
│   ├── persistence.md
│   ├── repository.md
│   ├── room-dao.md
│   └── room-database.md
│
├── async/
│   ├── coroutines.md
│   └── flow.md
│
├── testing/
│   └── testing.md
│
├── documentation/
│   └── guideline-format.md
│
└── project/
    ├── decisions.md
    └── exceptions.md
```

