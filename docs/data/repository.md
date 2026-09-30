# Repository

## Description

A repository provides a stable abstraction over application data.

It hides persistence and transport implementation details from consumers and coordinates access to one or more data sources when required.

Repository behavior must remain correct when operations overlap or execute concurrently.

## Rules

- Repository APIs expose domain-facing models.
- Persistence entities and network DTOs must not leave the data boundary.
- Prefer `Flow` for observable data.
- Prefer `suspend` for one-shot asynchronous operations.
- Keep mapping at architectural boundaries.
- Do not expose persistence- or transport-specific types through repository APIs.
- Do not create repositories solely to rename data-source methods unless the abstraction provides a meaningful boundary.
- Choose the persistence mechanism according to `docs/data/persistence.md`.
- Prefer atomic or transactional storage operations when multiple values must change consistently.
- Read-modify-write operations must remain correct when multiple mutations overlap.
- Do not derive concurrent mutations from stale cached state.
- Do not rely on UI-level disabling of actions for repository correctness.
- Do not report a mutation as successful before persistence has completed successfully.
- Moving work to a background dispatcher does not make compound operations atomic.
- Introduce explicit synchronization only when the underlying data source does not already provide the required guarantees.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/data/
├── ExampleRepository.kt
└── DefaultExampleRepository.kt
```

### Modular profile

```text
feature/example/src/main/java/<base-package>/feature/example/domain/
└── ExampleRepository.kt

core/data/src/main/java/<base-package>/core/data/example/
└── DefaultExampleRepository.kt
```

## Code

### `ExampleRepository.kt`

```kotlin
interface ExampleRepository {

    fun observeItems(): Flow<List<Example>>

    suspend fun save(
        item: Example,
    )

    suspend fun delete(
        itemId: String,
    )
}
```

### `DefaultExampleRepository.kt`

```kotlin
internal class DefaultExampleRepository(
    private val exampleDao: ExampleDao,
) : ExampleRepository {

    override fun observeItems(): Flow<List<Example>> =
        exampleDao.observeAll()
            .map { entities ->
                entities.map { entity ->
                    Example(
                        id = entity.id,
                        value = entity.value,
                    )
                }
            }

    override suspend fun save(
        item: Example,
    ) {
        exampleDao.upsert(
            ExampleEntity(
                id = item.id,
                value = item.value,
            ),
        )
    }

    override suspend fun delete(
        itemId: String,
    ) {
        exampleDao.deleteById(
            itemId = itemId,
        )
    }
}
```

## Gotchas

- Do not expose persistence entities or transport DTOs from repository APIs.
- Do not serialize structured record collections into a single preference value.
- Moving blocking work to `Dispatchers.IO` solves thread usage, not mutation safety.
- A `Mutex` can serialize custom mutations but should not compensate for an unsuitable persistence mechanism.
- UI-level duplicate-action prevention is not a substitute for repository correctness.
- Avoid maintaining a second mutable in-memory source of truth when the persistence layer already exposes observable data.
- Use storage-level transactions when multiple writes must succeed or fail together.