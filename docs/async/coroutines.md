# Coroutines

## Description

Coroutines follow the lifetime of their owner. The illustrative blocking-file adapter switches dispatcher inside the executing layer.

## Rules

- Never use `GlobalScope`.
- Do not create unscoped `CoroutineScope` instances without explicit ownership and cancellation.
- ViewModels use `viewModelScope`.
- Lower layers should expose suspendable work instead of starting caller-owned work internally.
- Suspend APIs in lower layers must be safe to call from the main thread.
- The layer performing blocking I/O moves it to an appropriate IO dispatcher; substantial CPU work uses an appropriate computation dispatcher.
- `suspend` does not change execution threads.
- Do not block the main thread or assume callers will compensate for unsafe APIs.
- Inject dispatchers only when control is required for correctness, isolation, or testing.
- Preserve cancellation, including in `try/catch`, `runCatching`, adapters, and retries.
- Broad exception handling must rethrow `CancellationException`.
- Define overlap policy for asynchronous operations: reject duplicates, serialize, or cancel/replace.
- Atomic state updates alone do not make a sequence of operations atomic.
- Handle expected operational failures at the owning boundary; unexpected child failures propagate according to structured concurrency and the scope's supervision policy.
- A `CoroutineExceptionHandler` is reporting, not recovery.
- Select work that must outlive a ViewModel using Background Work.
- Keep coroutine code in the class that owns the work.

## Code

```kotlin
suspend fun readFile(file: File, ioDispatcher: CoroutineDispatcher): String =
    withContext(ioDispatcher) { file.readText() }
```

## Gotchas

- `launch` does not return an operation result.
- Its failures still propagate to the parent or uncaught-exception handling; fire-and-forget is not error handling.
- New scopes inside repositories complicate cancellation and testing.
- `withContext(Dispatchers.IO)` is not automatically required for every suspend function.
- Do not accidentally convert `CancellationException` into an ordinary failure.
- Suspending Room or asynchronous network APIs do not automatically need an IO wrapper.

## References

- [Background Work](background-work.md)
- [ViewModel](../ui/viewmodel.md)
- [Repository](../data/repository.md)
- [Coroutine best practices](https://developer.android.com/kotlin/coroutines/coroutines-best-practices)
