# UI State

## Description

UI state is an immutable snapshot for one destination. The complete reference is shared by the ViewModel, screens, and tests.

## Rules

- Use immutable data classes.
- Prefer one coherent screen state over multiple unrelated observable properties.
- Provide sensible defaults.
- Do not store Android UI objects in state.
- Do not store callbacks in state.
- Do not store navigation destinations or navigation history in UI state.
- Use UI models when domain models are not suitable for direct rendering.
- Keep errors with the screen or operation that can meaningfully present or handle them.
- Every exposed error must have an intentional consumer.
- Clear transient errors when they are consumed or become irrelevant.
- Use operation-specific state when progress or failure affects UI behavior.
- Avoid exposing raw exceptions directly as presentation state.
- UI state must not be used as durable application persistence.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/ui/
└── ExampleUiState.kt
```

### Modular profile

```text
feature/example/src/main/java/<base-package>/feature/example/ui/
└── ExampleUiState.kt
```

## Code

### `ExampleUiState.kt`

```kotlin
package com.example.app.feature.example.ui

data class ExampleUiState(
    val isLoading: Boolean = true,
    val items: List<ExampleItemUiModel> = emptyList(),
    val query: String = "",
    val error: ExampleUiError? = null,
)

data class ExampleItemUiModel(
    val id: String,
    val label: String,
)

enum class ExampleUiError { LoadFailed }

sealed interface ExampleEvent {
    data object Retry : ExampleEvent
    data class QueryChanged(val query: String) : ExampleEvent
}
```

## Gotchas

- Do not use UI state to model the application navigation back stack.
- Do not expose errors that no current UI can present or handle.
- Multiple booleans can create impossible state combinations; use a more explicit state model when necessary.
- Do not expose mutable collections.
- Avoid storing values that are cheap and deterministic to derive.
- Important pending operation results remain state until acknowledged; the owning screen performs UI effects according to UI Events and Effects.
- Do not use UI state as durable application storage.

## References

- [ViewModel](viewmodel.md)
- [Screen](screen.md)
- [Testing](../testing/testing.md)
- [UI Events and Effects](events-and-effects.md)
