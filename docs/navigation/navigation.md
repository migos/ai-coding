# Navigation

## Description

Navigation 3 renders typed, saveable keys independently of screen UI state. The complete reference connects a root list and detail using an injected repository and entry-owned ViewModels.

## Rules

- Use Navigation 3 when the application contains multiple independent destinations.
- Model destinations using typed navigation keys.
- Use serializable navigation keys when the back stack must survive recreation.
- Prefer `rememberNavBackStack` for a saveable Navigation 3 back stack.
- Use `NavDisplay` to render destinations from the navigation back stack.
- Use the `entryProvider` DSL to map navigation keys to destination content.
- Application navigation state must not be represented as a `screen`, `destination`, or similar property inside ordinary UI state.
- ViewModels must not own the application navigation back stack.
- System and gesture back must operate on the navigation back stack.
- Do not use `BackHandler` to manually reproduce ordinary destination back behavior already represented by the back stack.
- Pass only the information required to identify or initialize a destination.
- Prefer stable identifiers over passing complete domain objects through navigation.
- Screens receive navigation callbacks instead of directly owning the navigation stack.
- Navigation decisions that depend purely on user interaction belong at the navigation boundary.
- Guard navigation against stale callbacks from hidden/transitioning entries and repeated interactions.
- Lifecycle gating and stack identity/deduplication are separate concerns.
- Scope screen ViewModels to their back-stack entry using the Navigation 3 ViewModel decorator, after the saveable-state decorator.
- Popping an entry clears its ViewModels; hiding an entry does not.
- Use owner-aware ViewModel factories/creation extras for `SavedStateHandle`.
- Navigation 3 arguments are passed explicitly from the key; they are not automatically injected into the handle.
- Preserve the root entry for in-app back actions.
- Let the platform handle leaving the app at the root; do not empty the stack.
- Define key identity deliberately.
- If multiple instances of the same logical destination/arguments are allowed on the stack, add a serializable unique instance ID so entry-owned state is not unintentionally shared.
- Handle operation-dependent navigation through acknowledged state/results according to UI Events and Effects.
- The user expects system back to return to the previous UI.
- It represents a distinct navigation step or user task.
- It requires its own navigation arguments.
- It can be opened independently from another destination.
- It has a meaningful identity in the application's back stack.
- The application composition root supplies the repository contract.
- For nested/multiple stacks or deep links, define entry identity, root/back behavior, restoration, and input validation.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/navigation/
├── AppRoute.kt
└── AppNavigation.kt
```

### Modular profile

```text
app/src/main/java/<base-package>/navigation/
├── AppRoute.kt
└── AppNavigation.kt
```

## Code

### `AppRoute.kt`

```kotlin
package com.example.app.navigation

import androidx.navigation3.runtime.NavKey
import kotlinx.serialization.Serializable

@Serializable
sealed interface AppRoute : NavKey {

    @Serializable
    data object Start : AppRoute

    @Serializable
    data class Destination(
        val id: String,
    ) : AppRoute
}
```

### `AppNavigation.kt`

```kotlin
package com.example.app.navigation

import androidx.compose.runtime.Composable
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.compose.LocalLifecycleOwner
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.lifecycle.viewmodel.navigation3.rememberViewModelStoreNavEntryDecorator
import androidx.navigation3.runtime.entryProvider
import androidx.navigation3.runtime.rememberNavBackStack
import androidx.navigation3.runtime.rememberSaveableStateHolderNavEntryDecorator
import androidx.navigation3.ui.NavDisplay
import com.example.app.feature.example.data.ExampleRepository
import com.example.app.feature.example.ui.ExampleDetailScreen
import com.example.app.feature.example.ui.ExampleScreen
import com.example.app.feature.example.ui.ExampleViewModel

@Composable
fun AppNavigation(repository: ExampleRepository) {
    val backStack = rememberNavBackStack(AppRoute.Start)

    NavDisplay(
        backStack = backStack,
        entryDecorators = listOf(
            rememberSaveableStateHolderNavEntryDecorator(),
            rememberViewModelStoreNavEntryDecorator(),
        ),
        onBack = {
            if (backStack.size > 1) backStack.removeLastOrNull()
        },
        entryProvider = entryProvider {
            entry<AppRoute.Start> {
                val lifecycle = LocalLifecycleOwner.current.lifecycle
                val model: ExampleViewModel = viewModel(
                    factory = ExampleViewModel.factory(repository),
                )
                ExampleScreen(
                    viewModel = model,
                    onItemClick = { id ->
                        val destination = AppRoute.Destination(id)
                        if (lifecycle.currentState.isAtLeast(Lifecycle.State.RESUMED) &&
                            backStack.lastOrNull() == AppRoute.Start &&
                            backStack.lastOrNull() != destination
                        ) {
                            backStack.add(destination)
                        }
                    },
                )
            }
            entry<AppRoute.Destination> { route ->
                val lifecycle = LocalLifecycleOwner.current.lifecycle
                val model: ExampleViewModel = viewModel(
                    factory = ExampleViewModel.factory(repository, itemId = route.id),
                )
                ExampleDetailScreen(
                    viewModel = model,
                    onBack = {
                        if (lifecycle.currentState.isAtLeast(Lifecycle.State.RESUMED) &&
                            backStack.lastOrNull() == route && backStack.size > 1
                        ) {
                            backStack.removeLastOrNull()
                        }
                    },
                )
            }
        },
    )
}
```

## Gotchas

- Do not replace a navigation back stack with `when (uiState.screen)`.
- Do not store navigation history inside a ViewModel.
- Do not pass large or mutable domain objects as navigation arguments.
- Do not use manual `BackHandler` logic to compensate for destinations that were not modeled as navigation destinations.
- Do not create a destination for every composable.
- Do not allow repeated click events to unintentionally push the same destination multiple times.
- Navigation state restoration depends on navigation keys being saveable when using a persistent back stack.

## References

- [UI Events and Effects](../ui/events-and-effects.md)
- [Screen](../ui/screen.md)
- [ViewModel](../ui/viewmodel.md)
- [Project Setup](../project/setup.md)
- [Navigation 3 saved state and ViewModel scoping](https://developer.android.com/guide/navigation/navigation-3/save-state)
