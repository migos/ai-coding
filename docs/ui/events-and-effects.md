# UI Events and Effects

## Description

UI callbacks express intent; ViewModels expose results and screens perform effects. Keep important results available until acknowledged, even when collection pauses.

## Rules

- Keep pure click navigation at the navigation boundary.
- For navigation depending on a save/validation result, wait for the documented successful result and let the owning active destination invoke its callback.
- Represent important pending results in observable state until the intended UI consumer acknowledges them.
- Use a result identity when a subsequent result could replace an earlier one; acknowledge only the matching result.
- If losing a result on process death is unacceptable, persist the operation/result or reconstruct it from durable state.
- `StateFlow` alone survives neither process death nor ViewModel removal.
- For multiple pending results, define queue/coalescing behavior explicitly.
- Do not overwrite an unconsumed result accidentally.
- Channels and replay-free SharedFlows are not a default delivery mechanism for critical results.
- Use them only when loss, buffering, replay, and consumer ownership are acceptable and documented.
- Consume results only while the owning destination is in the required active lifecycle state.
- Hidden/back-stack entries must not perform navigation or display competing UI.
- Use `LaunchedEffect` for composition-owned suspending effects, `DisposableEffect` for registrations needing cleanup, and `rememberCoroutineScope` for UI callback work.
- Do not execute side effects in a composable body.
- Effect keys describe what should restart the effect.
- Use `rememberUpdatedState` for changing callbacks/values that should not restart a long-lived effect.
- Acknowledge a pending result after its UI action succeeds.
- Navigation must be guarded against duplicate/stale callbacks.
- Do not claim exactly-once effects across crashes; define recovery/idempotency for important actions.
- Permissions and Activity results use the platform contracts, launched from the active UI boundary.
- Re-check permission state and handle denial/cancellation; do not launch dialogs during recomposition.
- Test completion while stopped, resumption, rotation, repeated callbacks, and consumer acknowledgement.
- Pending operation results are state; navigation and snackbar display are screen effects.

## Gotchas

- A lifecycle-aware collector does not automatically make a lossy event stream reliable.
- `LaunchedEffect(Unit)` does not mean once for the application's entire lifetime.
- Navigation 3 entry-owned `SavedStateHandle` is not automatically populated with typed route arguments; see Navigation.

## References

- [Navigation](../navigation/navigation.md)
- [UI events](https://developer.android.com/topic/architecture/ui-layer/events)
- [Compose side effects](https://developer.android.com/develop/ui/compose/side-effects)
