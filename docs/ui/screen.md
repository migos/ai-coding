# Screen

## Description

A screen composable is the UI entry point for a feature or destination.

It connects observable state and event callbacks to presentation UI.

## Rules

- Collect ViewModel state at the screen boundary.
- Pass immutable state and callbacks to reusable composables.
- Reusable composables must not receive a ViewModel.
- Composables must not call repositories, DAOs, or data sources directly.
- Keep navigation integration close to the screen boundary.
- Prefer explicit callbacks over mutable shared state.

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

- Do not launch coroutines directly from the composable body.
- Avoid passing `NavController` deep into the UI tree.
- Do not duplicate ViewModel state with unnecessary local `remember` state.
- Do not hide business rules in click handlers or visibility checks.
