# Engineering Instructions

This repository follows documented Android engineering standards intended for both humans and coding agents.

The documentation defines:
- architecture;
- dependency direction;
- file placement;
- reference implementations;
- known pitfalls;
- project-specific decisions and exceptions.

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

## Implementation workflow

Before writing code:

1. Confirm the active architecture profile.
2. Inspect existing implementations.
3. Read the relevant guidelines.
4. Confirm file placement.
5. Reuse existing abstractions where appropriate.
6. Prefer established project patterns over introducing new ones.

During implementation:

1. Keep changes scoped to the requested task.
2. Do not refactor unrelated code.
3. Follow dependency boundaries.
4. Keep public APIs as small as possible.
5. Add or update tests when behavior changes.
6. Avoid adding libraries unless necessary.
7. Do not introduce architectural layers or Gradle modules without a concrete reason.

After implementation:

1. Verify affected modules compile.
2. Run relevant tests.
3. Run formatting and static analysis if configured.
4. Verify file placement against the active architecture profile.
5. Verify documented dependency rules.
6. Report intentional deviations.

## Documentation map

### Architecture

- `docs/architecture/architecture.md`
- `docs/architecture/simple.md`
- `docs/architecture/modular.md`
- `docs/architecture/dependency-rules.md`
- `docs/architecture/migration-simple-to-modular.md`

### Documentation rules

- `docs/documentation/guideline-format.md`

### UI

- `docs/ui/screen.md`
- `docs/ui/viewmodel.md`
- `docs/ui/ui-state.md`

### Data

- `docs/data/repository.md`
- `docs/data/room-dao.md`
- `docs/data/room-database.md`

### Async

- `docs/async/coroutines.md`
- `docs/async/flow.md`

### Testing

- `docs/testing/testing.md`

### Project configuration

- `docs/project/decisions.md`
- `docs/project/exceptions.md`

## Guideline format

Engineering guideline files generally use:

1. **Description**
2. **Rules**
3. **Placement**
4. **Code**
5. **Gotchas**

Every concrete source file shown in a `Placement` tree must have a corresponding implementation in the `Code` section of the same guideline.

Reference code is normative unless overridden by a project-specific decision or exception.
