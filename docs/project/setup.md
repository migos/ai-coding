# Project Setup

## Description

Initialize Kotlin/Compose apps deliberately before generating code. The Code section contains illustrative decision and verification templates; replace placeholders before adopting them.

## Rules

- Read `decisions.md`, `exceptions.md`, the active architecture profile, and the dependency rules first.
- In an existing application, inspect its Gradle files, version catalog, manifest, CI, tests, and existing implementations before selecting APIs.
- Preserve recorded choices unless the task authorizes changing them.
- In a new application, choose the smallest suitable setup within the authorized requirements and record material choices.
- Surface missing product requirements or conflicting decisions before choices that depend on them; do not ask for approval for every routine setup detail.
- Record the architecture profile, language/UI stack, minimum/target/compile SDK, JDK, Gradle, Android Gradle Plugin, Kotlin, and DI approach.
- Pin compatible dependency/plugin versions in Gradle or a version catalog; decisions may link to that source rather than duplicate every version.
- Use Kotlin/Compose and Navigation 3 as template defaults.
- A different UI/navigation stack needs a project decision and replacement guidance for affected examples.
- Enable the Kotlin Compose compiler plugin and Kotlin serialization plugin with versions compatible with Kotlin.
- Record any KSP and Room compiler setup.
- A Compose BOM does not manage Lifecycle, Navigation 3, Room, or Kotlin plugin versions.
- Add only dependencies needed by the application.
- The complete reference set uses Compose runtime/foundation/Material 3, Lifecycle ViewModel/SavedState/ViewModel Compose/Runtime Compose, Navigation 3 runtime/UI, Lifecycle ViewModel Navigation 3, kotlinx serialization, coroutines, Room runtime/KTX/compiler, and JUnit 4/coroutines-test for local tests.
- For Room schema export, configure the schema directory through the Room Gradle plugin or supported compiler arguments, commit exported schemas, and configure migration tests.
- `exportSchema = true` alone is not schema-directory configuration.
- Record the application ID/namespace, build variants, and supported device/window configurations in project decisions.
- Put actual build/test/format commands, device-test prerequisites, and workflow restrictions in `AGENTS.md`.
- Keep secrets out of these records and build artifacts.
- Configure the object graph and infrastructure lifetimes according to Dependency Injection.
- Define production error reporting and logging without sensitive data.
- Configure manifest permissions, exported components, network policy, file sharing, and backup rules according to Security.
- Before feature work, verify the initial app builds and its configured baseline checks run.
- Report blocked prerequisites explicitly.
- Replace decision-template placeholders before recording active decisions.
- Adapt verification tasks to actual modules, variants, and configured tools.

## Code

### Decision entry

```md
## Dependency Injection

Decision: <manual composition root or selected framework>

Rationale: <why it fits the application>

Affected areas: object construction, scopes, ViewModel factories, tests.
```

### Verification commands

```text
Build: ./gradlew :app:assembleDebug
Local tests: ./gradlew :app:testDebugUnitTest
Static analysis: ./gradlew :app:lintDebug
Formatting: <configured task, or explicitly not configured>
Device tests: ./gradlew :app:connectedDebugAndroidTest
Prerequisites: recorded JDK, SDK, and device/API requirements
```

## Gotchas

- Template examples and unresolved placeholders must not be mistaken for active decisions.
- Choose APIs supported by resolved dependencies, not by whichever documentation version happens to be newest.
- A successful debug build does not verify release-only behavior, migrations, or process restoration.

## References

- [Agent Instructions](../../AGENTS.md)
- [Dependency Injection](../di/dependency-injection.md)
- [Security](../security/security.md)
