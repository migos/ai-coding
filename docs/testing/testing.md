# Testing

## Description

Tests protect observable behavior and important architectural assumptions.

Prefer deterministic tests that verify public behavior rather than private implementation details.

## Rules

- Add tests for non-trivial business logic.
- Add regression tests for fixed defects when practical.
- Test ViewModels through exposed state and events.
- Prefer fakes for simple owned interfaces.
- Mock only when a fake would be disproportionately expensive.
- Do not use arbitrary delays for synchronization.
- Coroutine and Flow tests must use test schedulers when timing matters.
- Keep tests close to the source ownership they verify.

## Placement

### Simple profile

```text
app/src/test/java/<base-package>/feature/example/
├── ExampleViewModelTest.kt
└── FakeExampleRepository.kt
```

### Modular profile

```text
feature/example/src/test/java/<base-package>/feature/example/
├── ExampleViewModelTest.kt
└── FakeExampleRepository.kt
```

## Code

### `FakeExampleRepository.kt`

```kotlin
class FakeExampleRepository : ExampleRepository {

    private val items = MutableStateFlow<List<Example>>(emptyList())

    override fun observeItems(): Flow<List<Example>> = items

    override suspend fun refresh() = Unit

    fun emit(value: List<Example>) {
        items.value = value
    }
}
```

### `ExampleViewModelTest.kt`

```kotlin
class ExampleViewModelTest {

    private val repository = FakeExampleRepository()

    @Test
    fun `items are exposed as ui state`() = runTest {
        val viewModel = ExampleViewModel(repository)

        repository.emit(
            listOf(
                Example(
                    id = "1",
                    value = "Example",
                ),
            ),
        )

        val state = viewModel.state
            .filter { !it.isLoading }
            .first()

        assertEquals(
            listOf(
                ExampleItemUiModel(
                    id = "1",
                    label = "Example",
                ),
            ),
            state.items,
        )
    }
}
```

## Gotchas

- Avoid `delay()` as synchronization in tests.
- Do not mock simple value objects.
- Tests that depend on private methods are tightly coupled to implementation.
- Flow tests can miss emissions when collection timing is uncontrolled.
- UI snapshot tests complement but do not replace behavior tests.
