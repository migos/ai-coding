# Navigation

## Description

Navigation represents movement between independent application destinations.

Navigation 3 is the default navigation solution for Compose applications using these standards.

Navigation state is modeled as a back stack of typed navigation keys. The application owns this back stack and `NavDisplay` resolves its entries into destination content.

Navigation state must remain separate from ordinary screen UI state.

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
- Prevent accidental duplicate navigation when an interaction can fire repeatedly.

A UI element should normally be modeled as an independent destination when one or more of the following apply:

- The user expects system back to return to the previous UI.
- It represents a distinct navigation step or user task.
- It requires its own navigation arguments.
- It can be opened independently from another destination.
- It has a meaningful identity in the application's back stack.

Reusable or subordinate UI that does not require navigation identity should remain part of its owning destination.

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

Feature modules expose destination content and navigation callbacks. The application-level navigation host composes these destinations into the application back stack.

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
import androidx.lifecycle.compose.dropUnlessResumed
import androidx.navigation3.runtime.entryProvider
import androidx.navigation3.runtime.rememberNavBackStack
import androidx.navigation3.ui.NavDisplay

@Composable
fun AppNavigation() {
    val backStack = rememberNavBackStack(
        AppRoute.Start,
    )

    NavDisplay(
        backStack = backStack,
        onBack = {
            backStack.removeLastOrNull()
        },
        entryProvider = entryProvider {
            entry<AppRoute.Start> {
                StartScreen(
                    onOpenDestination = dropUnlessResumed { id ->
                        backStack.add(
                            AppRoute.Destination(
                                id = id,
                            ),
                        )
                    },
                )
            }

            entry<AppRoute.Destination> { route ->
                DestinationScreen(
                    id = route.id,
                    onBack = {
                        backStack.removeLastOrNull()
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