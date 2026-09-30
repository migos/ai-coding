# Flow

## Description

Flow represents asynchronous streams of values.

Use cold flows when work should begin on collection. Use `StateFlow` when a component owns current observable state.

## Rules

- Expose immutable `Flow` or `StateFlow`.
- Keep mutable flow implementations private.
- Prefer transformation operators over unnecessary intermediate mutable state.
- Collect flows in lifecycle-aware scopes.
- Share expensive upstream work only when multiple collectors would otherwise duplicate it.
- Be explicit about error and retry behavior.
- Avoid nested long-lived `collect` calls.
- Do not convert every flow to `StateFlow` by default.

## Placement

This guideline defines behavior and does not create dedicated Flow source files.

Flows stay with the repository, ViewModel, or other class that owns or exposes them.

## Code

Repository stream:

```kotlin
interface ExampleRepository {
    fun observeItems(): Flow<List<Example>>
}
```

ViewModel state:

```kotlin
class ExampleViewModel(
    repository: ExampleRepository,
) : ViewModel() {

    val state: StateFlow<ExampleUiState> =
        repository.observeItems()
            .map { items ->
                ExampleUiState(
                    isLoading = false,
                    items = items.map { item ->
                        ExampleItemUiModel(
                            id = item.id,
                            label = item.value,
                        )
                    },
                )
            }
            .stateIn(
                scope = viewModelScope,
                started = SharingStarted.WhileSubscribed(5_000),
                initialValue = ExampleUiState(),
            )
}
```

## Gotchas

- Multiple collectors can duplicate upstream work.
- `catch` catches upstream exceptions, not exceptions thrown after it.
- `stateIn` and `shareIn` introduce lifetime and replay semantics.
- Infinite flows require explicit ownership and cancellation.
