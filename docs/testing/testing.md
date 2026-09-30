# Testing

## Description

Verify observable behavior with deterministic tests and simple fakes. The complete references cover item updates, failure/retry overlap, and saved-query behavior.

## Rules

- Add tests for non-trivial business logic.
- Add regression tests for fixed defects when practical.
- Test ViewModels through exposed state and events.
- Prefer simple fakes for owned interfaces.
- Mock only when a fake would be disproportionately expensive.
- Tests must be deterministic.
- Do not use arbitrary delays for synchronization.
- Coroutine and Flow tests must use test schedulers when timing matters.
- All test dispatchers in a test share one scheduler.
- Local JVM tests using `viewModelScope`/Main replace `Dispatchers.Main` before constructing the subject and reset it afterwards.
- `runTest` alone does not replace Main.
- Clear owned ViewModels/scopes and cancel long-lived test collectors.
- With `WhileSubscribed`, start a collector (for example in `backgroundScope`) before expecting upstream work; reading `.value` alone is not a subscription.
- Test failure paths when failures affect observable behavior.
- Test state restoration when losing temporary user state would be harmful.
- Test concurrent or overlapping mutations when correctness depends on mutation ordering or atomicity.
- Test navigation behavior when back-stack behavior is part of the feature contract.
- Test persistence migrations when a database schema changes.
- Test UI behavior that depends on content size when overflow or scrolling is relevant.
- Keep tests close to the source ownership they verify.
- Keep fakes and observation counters on the shared test scheduler.
- Build affected modules and run recorded test, lint, and format checks.
- Use platform/device checks for Android-dependent behavior.

## Selection

| Concern | Default verification |
|---|---|
| Pure Kotlin logic, repository fakes, ViewModel state/events | Local `src/test` with JUnit/coroutines-test |
| SQL/transactions and Room behavior | Actual Room database in a device test, or configured supported JVM/Android environment |
| Database schema upgrades and preserved records | Migration tests using exported schemas in the configured Android test environment |
| Compose semantics, navigation/back, insets/IME, scrolling | `src/androidTest` on supported devices/emulators; configured host tests may supplement |
| Saved-state recreation/process recovery | Owner-aware restoration tests plus targeted device recreation/process checks |

## Placement

### Simple profile

```text
app/src/test/java/<base-package>/feature/example/
├── ExampleViewModelTest.kt
├── FakeExampleRepository.kt
└── MainDispatcherRule.kt
```

### Modular profile

```text
feature/example/src/test/java/<base-package>/feature/example/
├── ExampleViewModelTest.kt
├── FakeExampleRepository.kt
└── MainDispatcherRule.kt
```

## Code

### `FakeExampleRepository.kt`

```kotlin
package com.example.app.feature.example

import com.example.app.feature.example.data.ExampleDataException
import com.example.app.feature.example.data.ExampleRepository
import com.example.app.feature.example.model.Example
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.emitAll
import kotlinx.coroutines.flow.flow
import kotlinx.coroutines.flow.update

class FakeExampleRepository : ExampleRepository {
    private val items = MutableStateFlow<List<Example>>(emptyList())
    var failureOnNextObservation: ExampleDataException? = null
    var observations = 0
        private set
    var activeObservations = 0
        private set

    // Observation counters/failure scripting belong to the single-threaded test scheduler.
    override fun observeItems(): Flow<List<Example>> = flow {
        observations++
        activeObservations++
        try {
            val failure = failureOnNextObservation
            failureOnNextObservation = null
            if (failure != null) throw failure
            emitAll(items)
        } finally {
            activeObservations--
        }
    }

    override suspend fun save(item: Example) {
        items.update { existing -> existing.filterNot { it.id == item.id }.plus(item) }
    }

    override suspend fun delete(itemId: String) {
        items.update { existing -> existing.filterNot { it.id == itemId } }
    }

    fun emit(value: List<Example>) {
        items.value = value.toList()
    }
}
```

### `ExampleViewModelTest.kt`

```kotlin
package com.example.app.feature.example

import androidx.lifecycle.SavedStateHandle
import androidx.lifecycle.ViewModelStore
import com.example.app.feature.example.data.ExampleDataException
import com.example.app.feature.example.model.Example
import com.example.app.feature.example.ui.ExampleEvent
import com.example.app.feature.example.ui.ExampleItemUiModel
import com.example.app.feature.example.ui.ExampleUiError
import com.example.app.feature.example.ui.ExampleViewModel
import java.io.IOException
import kotlinx.coroutines.ExperimentalCoroutinesApi
import kotlinx.coroutines.test.runCurrent
import kotlinx.coroutines.test.runTest
import org.junit.After
import org.junit.Assert.assertEquals
import org.junit.Assert.assertFalse
import org.junit.Assert.assertNull
import org.junit.Assert.assertTrue
import org.junit.Rule
import org.junit.Test

@OptIn(ExperimentalCoroutinesApi::class)
class ExampleViewModelTest {
    @get:Rule val mainDispatcherRule = MainDispatcherRule()
    private val repository = FakeExampleRepository()
    private val store = ViewModelStore()

    @After
    fun clearViewModels() {
        store.clear()
        mainDispatcherRule.dispatcher.scheduler.runCurrent()
        assertEquals(0, repository.activeObservations)
    }

    private fun model(handle: SavedStateHandle = SavedStateHandle()): ExampleViewModel =
        ExampleViewModel(repository, handle).also { store.put("example", it) }

    @Test
    fun `items are exposed and subsequent updates stay observable`() = runTest {
        val viewModel = model()
        repository.emit(listOf(Example("1", "Example")))
        runCurrent()
        assertFalse(viewModel.state.value.isLoading)
        assertEquals(listOf(ExampleItemUiModel("1", "Example")), viewModel.state.value.items)
        repository.emit(listOf(Example("2", "Updated")))
        runCurrent()
        assertEquals(listOf(ExampleItemUiModel("2", "Updated")), viewModel.state.value.items)
    }

    @Test
    fun `failure and repeated retry recover with only one active observation`() = runTest {
        repository.failureOnNextObservation = ExampleDataException(IOException("test failure"))
        val viewModel = model()
        runCurrent()
        assertEquals(ExampleUiError.LoadFailed, viewModel.state.value.error)
        assertEquals(0, repository.activeObservations)
        viewModel.onEvent(ExampleEvent.QueryChanged("recover"))
        assertEquals("recover", viewModel.state.value.query)

        viewModel.onEvent(ExampleEvent.Retry)
        viewModel.onEvent(ExampleEvent.Retry)
        assertTrue(viewModel.state.value.isLoading)
        assertNull(viewModel.state.value.error)
        repository.emit(listOf(Example("1", "Recovered")))
        runCurrent()
        assertNull(viewModel.state.value.error)
        assertEquals(listOf(ExampleItemUiModel("1", "Recovered")), viewModel.state.value.items)
        assertEquals(2, repository.observations)
        assertEquals(1, repository.activeObservations)

        viewModel.onEvent(ExampleEvent.Retry)
        runCurrent()
        assertEquals(2, repository.observations)
    }

    @Test
    fun `saved query is restored and edits update the handle`() = runTest {
        val handle = SavedStateHandle(mapOf("query" to "keep"))
        val viewModel = model(handle)
        repository.emit(listOf(Example("1", "Keep"), Example("2", "Other")))
        runCurrent()
        assertEquals("keep", viewModel.state.value.query)
        assertEquals(listOf(ExampleItemUiModel("1", "Keep")), viewModel.state.value.items)

        viewModel.onEvent(ExampleEvent.QueryChanged("other"))
        runCurrent()
        assertEquals("other", handle.get<String>("query"))
        assertEquals(listOf(ExampleItemUiModel("2", "Other")), viewModel.state.value.items)
    }
}
```

### `MainDispatcherRule.kt`

```kotlin
package com.example.app.feature.example

import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.ExperimentalCoroutinesApi
import kotlinx.coroutines.test.StandardTestDispatcher
import kotlinx.coroutines.test.TestDispatcher
import kotlinx.coroutines.test.resetMain
import kotlinx.coroutines.test.setMain
import org.junit.rules.TestWatcher
import org.junit.runner.Description

@OptIn(ExperimentalCoroutinesApi::class)
class MainDispatcherRule(
    val dispatcher: TestDispatcher = StandardTestDispatcher(),
) : TestWatcher() {
    override fun starting(description: Description) {
        Dispatchers.setMain(dispatcher)
    }

    override fun finished(description: Description) {
        Dispatchers.resetMain()
    }
}
```

## Gotchas

- Avoid `delay()` as synchronization in tests.
- Do not mock simple value objects.
- Tests that depend on private methods are tightly coupled to implementation.
- Happy-path tests alone do not protect failure behavior.
- Sequential mutation tests do not reveal concurrency problems.
- Short fixture content may not reveal overflow or scrolling issues.
- Compilation success does not replace behavior tests.
- Do not write tests that merely repeat the implementation line by line.
- Plain JVM tests have no functioning Android UI thread.
- Do not replace Main in device tests.
- Fakes do not verify SQL, migrations, owner-aware factories, or process restoration.
- SavedStateHandle map tests do not verify Android process-death recovery.

## References

- [Project Setup](../project/setup.md)
- [Testing coroutines](https://developer.android.com/kotlin/coroutines/test)
- [ViewModel](../ui/viewmodel.md)
