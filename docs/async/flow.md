# Flow

## Description

Flows represent asynchronous values; StateFlow represents owned current state. The illustrative subscriber-owned fragment uses a private `MutableStateFlow<Long>` named `retryGeneration`, incremented on retry, and the shared repository/UI types.

## Rules

- Use cold Flow when work should start on collection.
- Use StateFlow when a component owns current state.
- Expose immutable `Flow` or `StateFlow`.
- Keep mutable flow implementations private.
- Prefer transformation operators over unnecessary intermediate mutable state.
- Collect flows in lifecycle-aware scopes.
- Share expensive upstream work only when multiple collectors would otherwise duplicate it.
- Be explicit about error and retry behavior.
- Handle expected upstream errors before `stateIn`/`shareIn`; StateFlow cannot represent failure without an explicit state value.
- Choose whether observation lasts for the ViewModel or only for active subscribers.
- Document stop timeout/replay behavior and whether stale data stays visible when resubscribing.
- Place mapping before the `catch` intended to handle its expected failures.
- Preserve cancellation; unexpected defects require the project's reporting policy.
- Avoid nested long-lived `collect` calls.
- Do not convert every flow to `StateFlow` by default.
- Incrementing the retry generation replaces the previous inner collection.
- Choose stop timeout and replay deliberately; `5_000` is an example policy.
- Preserve saved-query and destination filtering when adapting the canonical ViewModel.

## Code

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
val state: StateFlow<ExampleUiState> = retryGeneration
    .flatMapLatest {
        repository.observeItems()
            .map { items ->
                ExampleUiState(
                    isLoading = false,
                    items = items.map { ExampleItemUiModel(it.id, it.value) },
                )
            }
            .onStart { emit(ExampleUiState()) }
            .catch { failure ->
                if (failure !is ExampleDataException) throw failure
                emit(ExampleUiState(isLoading = false, error = ExampleUiError.LoadFailed))
            }
    }
    .stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5_000),
        initialValue = ExampleUiState(),
    )
```

## Gotchas

- Multiple collectors can duplicate upstream work.
- `catch` catches upstream exceptions, not exceptions thrown after it.
- `stateIn` and `shareIn` introduce lifetime and replay semantics.
- Infinite flows require explicit ownership and cancellation.
- The fragment omits the canonical ViewModel's query and destination filter.

## References

- [Repository](../data/repository.md)
- [ViewModel](../ui/viewmodel.md)
- [stateIn lifetime and exceptions](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/state-in.html)
