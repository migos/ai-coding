# Screen

## Description

A screen composable is the UI entry point for a navigation destination.

It connects observable state and event callbacks to presentation UI.

Independent navigation destinations should have independent top-level screen composables.

## Rules

- Collect ViewModel state at the screen boundary.
- Pass immutable state and callbacks to reusable composables.
- Reusable composables must not receive a ViewModel.
- Composables must not call repositories, DAOs, or data sources directly.
- Keep navigation integration at the screen boundary.
- Prefer explicit navigation callbacks over passing navigation infrastructure into the UI tree.
- Prefer explicit event callbacks over mutable shared state.
- Independent navigation destinations must not be implemented as UI-state branches inside another screen.
- Content that can exceed the available viewport must be scrollable.
- Use lazy containers for potentially large collections.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/ui/
└── ExampleScreen.kt
```

### Modular profile

```text
feature/example/src/main/java/<base-package>/feature/example/ui/
└── ExampleScreen.kt
```

## Code

### `ExampleScreen.kt`

```kotlin
@Composable
fun ExampleScreen(
    viewModel: ExampleViewModel,
    onItemClick: (String) -> Unit,
) {
    val state by viewModel.state.collectAsStateWithLifecycle()

    ExampleContent(
        state = state,
        onEvent = viewModel::onEvent,
        onItemClick = onItemClick,
    )
}

@Composable
private fun ExampleContent(
    state: ExampleUiState,
    onEvent: (ExampleEvent) -> Unit,
    onItemClick: (String) -> Unit,
) {
    when {
        state.isLoading -> CircularProgressIndicator()

        state.errorMessage != null -> {
            Column {
                Text(text = state.errorMessage)
                Button(
                    onClick = { onEvent(ExampleEvent.Retry) },
                ) {
                    Text("Retry")
                }
            }
        }

        else -> {
            LazyColumn {
                items(
                    items = state.items,
                    key = { it.id },
                ) { item ->
                    TextButton(
                        onClick = { onItemClick(item.id) },
                    ) {
                        Text(item.label)
                    }
                }
            }
        }
    }
}
```

## Gotchas

- Do not combine multiple independent destinations into one screen and switch between them using UI state.
- Do not launch coroutines directly from the composable body.
- Avoid passing navigation infrastructure such as a navigation stack deep into the UI tree.
- Do not duplicate ViewModel state with unnecessary local `remember` state.
- Do not hide business rules in click handlers or visibility checks.
- Do not assume content fits because sample or test data is short.
- Use `LazyColumn` or another lazy container for potentially large collections.
- Use a scrollable container for non-list content that may exceed the viewport.