# ViewModel

## Description

A ViewModel owns screen-level presentation state and coordinates application behavior.

It translates application data into UI state and receives UI events.

A ViewModel must not own application navigation state or directly depend on persistence and transport implementations.

## Rules

- Expose immutable state.
- Keep mutable state private.
- Prefer `StateFlow` for observable screen state.
- Launch asynchronous work in `viewModelScope`.
- Do not expose `MutableStateFlow`.
- Do not store `Activity`, `View`, navigation infrastructure, or composable references.
- Do not model the application navigation back stack inside ViewModel state.
- Keep reusable business logic outside the ViewModel.
- Do not access DAOs, databases, or network clients directly.
- Use repositories or domain abstractions for data access.
- Use `SavedStateHandle` for small ViewModel-owned state that should survive recreation.
- Do not use `SavedStateHandle` as durable persistence.
- Expose operation progress when the UI needs to prevent or communicate repeated actions.
- Report operation success only after the underlying operation has completed successfully.
- Map failures into presentation state that the owning screen can intentionally present or handle.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/ui/
└── ExampleViewModel.kt
```

### Modular profile

```text
feature/example/src/main/java/<base-package>/feature/example/ui/
└── ExampleViewModel.kt
```

## Code

### `ExampleViewModel.kt`

```kotlin
class ExampleViewModel(
    private val repository: ExampleRepository,
) : ViewModel() {

    private val _state = MutableStateFlow(ExampleUiState())
    val state: StateFlow<ExampleUiState> = _state.asStateFlow()

    init {
        observeItems()
    }

    fun onEvent(event: ExampleEvent) {
        when (event) {
            ExampleEvent.Retry -> observeItems()
        }
    }

    private fun observeItems() {
        viewModelScope.launch {
            repository.observeItems()
                .catch {
                    _state.update { state ->
                        state.copy(
                            isLoading = false,
                            errorMessage = "Unable to load data",
                        )
                    }
                }
                .collect { items ->
                    _state.update { state ->
                        state.copy(
                            isLoading = false,
                            items = items.map { item ->
                                ExampleItemUiModel(
                                    id = item.id,
                                    label = item.value,
                                )
                            },
                            errorMessage = null,
                        )
                    }
                }
        }
    }
}
```

## Gotchas

- Do not store navigation destinations or navigation history in a ViewModel.
- Do not access persistence or network implementations directly from a ViewModel.
- Starting the same long-lived collection repeatedly can create duplicate collectors.
- Avoid one ViewModel owning unrelated screens or destinations.
- Do not hide reusable business logic inside private ViewModel methods.
- `SavedStateHandle` is intended for restoration, not durable application storage.
- Do not expose operation success before the underlying work has actually succeeded.
- Do not expose failures that the owning screen cannot present or handle.