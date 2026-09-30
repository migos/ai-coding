# UI State

## Description

UI state is the immutable representation required to render a screen at a point in time.

The same state should produce the same UI.

## Rules

- Use immutable data classes.
- Prefer one coherent screen state over multiple unrelated observable properties.
- Provide sensible defaults.
- Do not store Android UI objects in state.
- Do not store callbacks in state.
- Use UI models when domain models are not suitable for direct rendering.
- Avoid raw exceptions as user-facing state.

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

- Multiple booleans can create impossible state combinations.
- Do not expose mutable collections.
- Avoid storing values that are cheap and deterministic to derive.
- Keep transient effects separate when they are not part of renderable state.
