# ViewModel

## Description

A ViewModel owns destination-level presentation state and coordinates behavior. The complete reference restores the query through `SavedStateHandle` and receives its immutable item ID from the navigation key.

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
- Map expected operational failures into presentation state that the owning screen can intentionally present or handle; preserve cancellation and surface unexpected defects through the project's failure-reporting policy.
- Own at most one collection for each logical observation.
- Serialize UI events on the main thread and explicitly guard or replace overlapping operations.
- Create ViewModels through the destination owner and owner-aware factory, not inside the composable body with a constructor.
- Follow Navigation and Dependency Injection.
- Retry restarts only a completed or failed observation.
- Give refresh operations their own progress, error, and overlap policy.
- Use repository queries or paging when in-memory filtering exceeds expected data limits.
- The reference observes until its ViewModel is cleared.
- Use subscriber-owned sharing when expensive observation should stop with the UI.

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
package com.example.app.feature.example.ui

import androidx.annotation.MainThread
import androidx.lifecycle.SavedStateHandle
import androidx.lifecycle.ViewModel
import androidx.lifecycle.createSavedStateHandle
import androidx.lifecycle.viewModelScope
import androidx.lifecycle.viewmodel.initializer
import androidx.lifecycle.viewmodel.viewModelFactory
import com.example.app.feature.example.data.ExampleDataException
import com.example.app.feature.example.data.ExampleRepository
import kotlinx.coroutines.Job
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.flow.catch
import kotlinx.coroutines.flow.combine
import kotlinx.coroutines.flow.update
import kotlinx.coroutines.launch

class ExampleViewModel(
    private val repository: ExampleRepository,
    private val savedStateHandle: SavedStateHandle,
    private val itemId: String? = null,
) : ViewModel() {
    private val query = savedStateHandle.getStateFlow("query", "")
    private val _state = MutableStateFlow(ExampleUiState(query = query.value))
    val state: StateFlow<ExampleUiState> = _state.asStateFlow()
    private var observationJob: Job? = null

    init {
        observeItems()
    }

    @MainThread
    fun onEvent(event: ExampleEvent) {
        when (event) {
            ExampleEvent.Retry -> observeItems()
            is ExampleEvent.QueryChanged -> {
                savedStateHandle["query"] = event.query
                // Keep controlled input responsive even before data arrives or after failure.
                _state.update { it.copy(query = event.query) }
            }
        }
    }

    @MainThread
    private fun observeItems() {
        if (observationJob?.isActive == true) return
        _state.update { it.copy(isLoading = true, error = null) }
        observationJob = viewModelScope.launch {
            combine(repository.observeItems(), query) { items, filter ->
                ExampleUiState(
                    isLoading = false,
                    query = filter,
                    items = items
                        .filter { item -> itemId == null || item.id == itemId }
                        .filter { item -> item.value.contains(filter, ignoreCase = true) }
                        .map { item -> ExampleItemUiModel(item.id, item.value) },
                )
            }
                .catch { failure ->
                    if (failure !is ExampleDataException) throw failure
                    emit(_state.value.copy(isLoading = false, error = ExampleUiError.LoadFailed))
                }
                .collect { next -> _state.value = next }
        }
    }

    companion object {
        fun factory(repository: ExampleRepository, itemId: String? = null) =
            viewModelFactory {
                initializer {
                    ExampleViewModel(
                        repository = repository,
                        savedStateHandle = createSavedStateHandle(),
                        itemId = itemId,
                    )
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
- Hiding a back-stack entry does not clear its ViewModel.

## References

- [Navigation](../navigation/navigation.md)
- [Dependency Injection](../di/dependency-injection.md)
- [Repository](../data/repository.md)
- [UI State](ui-state.md)
- [Flow](../async/flow.md)
