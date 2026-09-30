# Background Work

## Description

Choose work ownership by the required lifetime and completion guarantees, not by where a button happens to start it.

## Rules

- Decide whether cancellation when leaving a destination is acceptable before choosing a scope.
- Do not promise completion across process death from an application scope.
- WorkManager schedules persistent work; it does not guarantee immediate execution or exactly-once external effects.
- Use constraints, unique work policy, bounded retry/backoff, and observable progress when they belong to the operation contract.
- Distinguish permanent failure from retryable failure and cancellation.
- Persist the operation identity and only the small inputs needed to resume.
- Keep large payloads/files in appropriate storage, not WorkManager input data.
- Make repeated worker execution safe.
- Use server-supported idempotency keys or another defined deduplication protocol for remote writes, and atomic local state transitions.
- Ensure account changes, logout, and user cancellation prevent obsolete work from using another account's data or credentials.
- Use task-specific APIs for alarms, media, downloads, or other specialized work where appropriate.
- A foreground service requires a valid use case, current platform permissions/restrictions, and user-visible behavior; it is not a generic workaround.
- Test interruption, retry, duplicate scheduling, and completion/failure presentation when they affect behavior.

## Selection

| Required lifetime | Default owner |
|---|---|
| Relevant only while a screen ViewModel exists | `viewModelScope` |
| Relevant only while a composable/effect exists | Lifecycle-bound Compose effect or callback scope |
| Must outlive a destination, but may stop with the process | Explicitly owned application/account scope |
| Persistent, deferrable work that must be rescheduled across process/device restarts | WorkManager, when suitable |
| A specialized platform task | Its task-specific API; document why it fits |

## Gotchas

- Screen navigation can cancel an upload tied to a popped entry's ViewModel.
- Retrying after a timeout can repeat a write that already reached the server.
- Background execution constraints are version-sensitive; verify requirements against the project's target SDK and current official guidance.

## References

- [Background work](https://developer.android.com/develop/background-work)
- [Persistent work](https://developer.android.com/develop/background-work/background-tasks/persistent)
