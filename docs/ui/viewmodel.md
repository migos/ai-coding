# ViewModel

## Description

A ViewModel owns screen-level presentation state and coordinates application behavior.

It translates application data into UI state and receives UI events.

## Rules

- Expose immutable state.
- Keep mutable state private.
- Prefer `StateFlow` for observable screen state.
- Launch asynchronous work in `viewModelScope`.
- Do not expose `MutableStateFlow`.
- Do not store `Activity`, `View`, `NavController`, or composable references.
- Keep reusable business logic outside the ViewModel.
- Do not access DAOs or HTTP clients directly.

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

    private var observeJob: Job? = null

    init {
        observeItems()
    }

    fun onEvent(event: ExampleEvent) {
        when (event) {
            ExampleEvent.Retry -> observeItems()
        }
    }

    private fun observeItems() {
        observeJob?.cancel()

        observeJob = viewModelScope.launch {
            repository.observeItems()
                .catch { throwable ->
                    _state.update {
                        it.copy(
                            isLoading = false,
                            errorMessage = throwable.message ?: "Unknown error",
                        )
                    }
                }
                .collect { items ->
                    _state.update {
                        it.copy(
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

- Starting the same long-lived collection repeatedly can create duplicate collectors.
- Avoid one ViewModel owning unrelated screens.
- Do not hide reusable business logic inside private ViewModel methods.
- Raw exception messages are only boilerplate here; production projects should map errors to user-safe messages.
