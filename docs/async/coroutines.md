# Coroutines

## Description

Coroutines are the default mechanism for asynchronous work.

Coroutine ownership should follow lifecycle ownership.

## Rules

- Never use `GlobalScope`.
- Do not create unscoped `CoroutineScope` instances without explicit ownership and cancellation.
- ViewModels use `viewModelScope`.
- Lower layers should expose suspendable work instead of starting caller-owned work internally.
- Do not block coroutine threads with synchronous I/O.
- Inject dispatchers only when control is required for correctness, isolation, or testing.
- Preserve cancellation.

## Placement

This guideline defines behavior and does not create dedicated coroutine source files.

Coroutine code stays in the class that owns the work.

## Code

Preferred ownership:

```kotlin
class ExampleViewModel(
    private val repository: ExampleRepository,
) : ViewModel() {

    fun refresh() {
        viewModelScope.launch {
            repository.refresh()
        }
    }
}
```

Suspendable lower-layer API:

```kotlin
interface ExampleRepository {
    suspend fun refresh()
}
```

## Gotchas

- `launch` hides failures from the caller; use it only when the current owner intentionally owns the coroutine.
- New scopes inside repositories complicate cancellation and testing.
- `withContext(Dispatchers.IO)` is not automatically required for every suspend function.
- Do not accidentally convert `CancellationException` into an ordinary failure.
