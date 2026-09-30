# Engineering Standards

## Description

Shared standards for Kotlin/Compose Android applications. The architecture profile controls physical structure; both profiles use the same correctness baseline.

## Rules

- Read project decisions, exceptions, dependency rules, and the active profile before implementation.
- Follow every applicable row in Selection; a task can span multiple rows.
- Inspect existing implementations before choosing patterns or file placement.
- Initialize new apps using Project Setup before generating application code.
- Use only `simple` or `modular` unless explicitly authorized to add another profile.
- Explicit user instructions take precedence over repository defaults.
- Project instructions, documented decisions, and scoped exceptions take precedence over shared defaults.
- Exceptions override only their named rule and scope.
- The active profile takes precedence over shared guidelines.
- Existing conventions apply only when they comply with documented rules.
- Use general Android/Kotlin conventions where project standards leave a gap.
- Within the same precedence level, the more specific rule wins.
- Surface conflicting active instructions before making a dependent choice.
- Existing code does not justify repeating defects or violating boundaries.
- Preserve the recorded stack unless the task authorizes changing it.
- Choose the simplest implementation that meets all documented correctness requirements.
- Application size does not justify weaker navigation, persistence, lifecycle, or concurrency behavior.
- Keep changes scoped; avoid unrelated refactoring and migrations.
- Reuse established abstractions and keep public APIs small.
- Add libraries, layers, or modules only for a concrete need.
- Select APIs by required guarantees, not implementation convenience.
- Preserve dependency boundaries and standard platform solutions.
- Data correctness must not depend on UI-level duplicate-action prevention.
- Add or update behavior tests when behavior changes.
- Build affected modules and run relevant tests plus configured formatting/static analysis.
- Verify file placement and dependency direction against the active profile.
- Verify affected navigation, persistence, and state restoration behavior.
- Verify lifecycle scopes, overlapping mutations, and intentional failure handling.
- Verify applicable overflow, accessibility, UI-baseline, and security requirements.
- Use the project's recorded verification commands.
- Report intentional deviations, checks run, and validation limitations.
- Never report unexecuted checks as passed or silently weaken checks because prerequisites are unavailable.

## Selection

| Change | Read additionally |
|---|---|
| New project or build/toolchain | Project Setup; Testing |
| Screen, UI state, or ViewModel | Screen; UI State; ViewModel; UI Baseline; UI Events and Effects; Coroutines; Flow; Testing |
| Destinations, arguments, or back behavior | Navigation; UI Events and Effects; Testing |
| Repository or persistence | Repository; Persistence; Dependency Injection; Testing; Room DAO and Room Database when applicable |
| Network or local/remote synchronization | Network and Synchronization; Repository; Coroutines; Background Work; Security; Testing |
| Coroutine, Flow, or background task | Coroutines; Flow; Background Work; Testing |
| Object construction, bindings, or scopes | Dependency Injection; affected component guidelines |
| Sensitive data, permissions, external inputs, manifest, backup, or file sharing | Security; affected component guidelines |
| Standards or reference examples | Authoring Rules; affected guidelines |

## References

- [Agent Instructions](../AGENTS.md)
- [Authoring Rules](../CONTRIBUTING.md)
- [Project Decisions](project/decisions.md)
- [Project Exceptions](project/exceptions.md)
- [Project Setup](project/setup.md)
- [Architecture](architecture/architecture.md)
- [Simple Profile](architecture/simple.md)
- [Modular Profile](architecture/modular.md)
- [Dependency Rules](architecture/dependency-rules.md)
- [Incremental Migration](architecture/migration-simple-to-modular.md)
- [Navigation](navigation/navigation.md)
- [Screen](ui/screen.md)
- [UI State](ui/ui-state.md)
- [ViewModel](ui/viewmodel.md)
- [UI Baseline](ui/ui-baseline.md)
- [UI Events and Effects](ui/events-and-effects.md)
- [Repository](data/repository.md)
- [Persistence](data/persistence.md)
- [Room DAO](data/room-dao.md)
- [Room Database](data/room-database.md)
- [Network and Synchronization](data/network.md)
- [Coroutines](async/coroutines.md)
- [Flow](async/flow.md)
- [Background Work](async/background-work.md)
- [Dependency Injection](di/dependency-injection.md)
- [Security](security/security.md)
- [Testing](testing/testing.md)
