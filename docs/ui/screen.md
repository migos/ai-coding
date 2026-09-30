# Screen

## Description

Screens connect destination state and callbacks to reusable presentation content. The complete references use Simple-profile packages and resources; Navigation creates their entry-owned ViewModels.

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
- In a modular feature, import its generated `R` rather than the app module's `R`.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/ui/
├── ExampleScreen.kt
└── ExampleDetailScreen.kt
```

```text
app/src/main/res/values/
└── strings.xml
```

### Modular profile

```text
feature/example/src/main/java/<base-package>/feature/example/ui/
├── ExampleScreen.kt
└── ExampleDetailScreen.kt
```

```text
feature/example/src/main/res/values/
└── strings.xml
```

## Code

### `ExampleScreen.kt`

```kotlin
package com.example.app.feature.example.ui

import androidx.compose.foundation.layout.consumeWindowInsets
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.imePadding
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material3.CircularProgressIndicator
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.material3.TextButton
import androidx.compose.material3.TextField
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.res.stringResource
import androidx.lifecycle.compose.collectAsStateWithLifecycle
import com.example.app.R

@Composable
fun ExampleScreen(
    viewModel: ExampleViewModel,
    onItemClick: (String) -> Unit,
) {
    val state by viewModel.state.collectAsStateWithLifecycle()
    ExampleContent(state, viewModel::onEvent, onItemClick = onItemClick)
}

@Composable
internal fun ExampleContent(
    state: ExampleUiState,
    onEvent: (ExampleEvent) -> Unit,
    onItemClick: ((String) -> Unit)? = null,
    onBack: (() -> Unit)? = null,
) {
    Scaffold { padding ->
        LazyColumn(
            modifier = Modifier.fillMaxSize()
                .padding(padding)
                .consumeWindowInsets(padding)
                .imePadding(),
        ) {
            if (onBack != null) {
                item(key = "back") {
                    TextButton(onClick = onBack) { Text(stringResource(R.string.example_back)) }
                }
            }
            item(key = "query") {
                TextField(
                    value = state.query,
                    onValueChange = { onEvent(ExampleEvent.QueryChanged(it)) },
                    label = { Text(stringResource(R.string.example_filter)) },
                )
            }
            when {
                state.isLoading -> item(key = "loading") { CircularProgressIndicator() }
                state.error != null -> {
                    item(key = "error") {
                        val message = when (state.error) {
                            ExampleUiError.LoadFailed -> R.string.example_load_failed
                        }
                        Text(stringResource(message))
                    }
                    item(key = "retry") {
                        TextButton(onClick = { onEvent(ExampleEvent.Retry) }) {
                            Text(stringResource(R.string.example_retry))
                        }
                    }
                }
                state.items.isEmpty() -> item(key = "empty") {
                    Text(stringResource(R.string.example_empty))
                }
                else -> items(state.items, key = { "record:${it.id}" }) { item ->
                    if (onItemClick == null) {
                        Text(item.label)
                    } else {
                        TextButton(onClick = { onItemClick(item.id) }) { Text(item.label) }
                    }
                }
            }
        }
    }
}
```

### `ExampleDetailScreen.kt`

```kotlin
package com.example.app.feature.example.ui

import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.lifecycle.compose.collectAsStateWithLifecycle

@Composable
fun ExampleDetailScreen(viewModel: ExampleViewModel, onBack: () -> Unit) {
    val state by viewModel.state.collectAsStateWithLifecycle()
    ExampleContent(state, viewModel::onEvent, onBack = onBack)
}
```

### `strings.xml`

```xml
<resources>
    <string name="example_back">Back</string>
    <string name="example_filter">Filter</string>
    <string name="example_load_failed">Unable to load data</string>
    <string name="example_retry">Retry</string>
    <string name="example_empty">No matching items</string>
</resources>
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

## References

- [ViewModel](viewmodel.md)
- [UI State](ui-state.md)
- [UI Baseline](ui-baseline.md)
