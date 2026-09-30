# Testing

## Description

Tests protect observable behavior and important architectural assumptions.

Prefer deterministic tests that verify public behavior over tests coupled to implementation details.

Regression tests should be added when a defect reveals behavior that must remain correct in the future.

## Rules

- Add tests for non-trivial business logic.
- Add regression tests for fixed defects when practical.
- Test ViewModels through exposed state and events.
- Prefer simple fakes for owned interfaces.
- Mock only when a fake would be disproportionately expensive.
- Tests must be deterministic.
- Do not use arbitrary delays for synchronization.
- Coroutine and Flow tests must use test schedulers when timing matters.
- Test failure paths when failures affect observable behavior.
- Test state restoration when losing temporary user state would be harmful.
- Test concurrent or overlapping mutations when correctness depends on mutation ordering or atomicity.
- Test navigation behavior when back-stack behavior is part of the feature contract.
- Test persistence migrations when a database schema changes.
- Test UI behavior that depends on content size when overflow or scrolling is relevant.
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

    private val items = MutableStateFlow<List<Example>>(
        emptyList(),
    )

    override fun observeItems(): Flow<List<Example>> =
        items

    override suspend fun save(
        item: Example,
    ) {
        items.value = items.value
            .filterNot { existing ->
                existing.id == item.id
            }
            .plus(item)
    }

    override suspend fun delete(
        itemId: String,
    ) {
        items.value = items.value
            .filterNot { item ->
                item.id == itemId
            }
    }

    fun emit(
        value: List<Example>,
    ) {
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
        val viewModel = ExampleViewModel(
            repository = repository,
        )

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
- Happy-path tests alone do not protect failure behavior.
- Sequential mutation tests do not reveal concurrency problems.
- Short fixture content may not reveal overflow or scrolling issues.
- Compilation success does not replace behavior tests.
- Do not write tests that merely repeat the implementation line by line.