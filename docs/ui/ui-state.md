# UI State

## Description

UI state is the immutable representation required to render a screen at a point in time.

The same state should produce the same UI.

UI state belongs to the screen or destination that consumes it. Navigation state, persistence state, and unrelated destination state should not be mixed into the same UI state.

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
data class ExampleUiState(
    val isLoading: Boolean = true,
    val items: List<ExampleItemUiModel> = emptyList(),
    val errorMessage: String? = null,
)

data class ExampleItemUiModel(
    val id: String,
    val label: String,
)

sealed interface ExampleEvent {
    data object Retry : ExampleEvent
}
```

## Gotchas

- Do not use UI state to model the application navigation back stack.
- Do not expose errors that no current UI can present or handle.
- Multiple booleans can create impossible state combinations; use a more explicit state model when necessary.
- Do not expose mutable collections.
- Avoid storing values that are cheap and deterministic to derive.
- Keep transient effects separate when they are not part of renderable state.
- Do not use UI state as durable application storage.