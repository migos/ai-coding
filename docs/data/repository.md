# Repository

## Description

A repository provides a stable abstraction over application data.

It hides persistence and transport implementation details from consumers.

## Rules

- Repository APIs expose domain-facing models.
- Room entities and network DTOs must not leave the data boundary.
- Prefer `Flow` for observable data.
- Prefer `suspend` for one-shot asynchronous operations.
- Keep mapping at architectural boundaries.
- Do not expose framework-specific result types through repository APIs.
- Do not create repositories solely to rename DAO methods unless the abstraction provides a useful boundary.

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

    suspend fun refresh()
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

    override suspend fun refresh() {
        val current = exampleDao.getAll()

        if (current.isEmpty()) {
            exampleDao.upsertAll(
                listOf(
                    ExampleEntity(
                        id = "example",
                        value = "Example",
                    ),
                ),
            )
        }
    }
}
```

## Gotchas

- Do not expose `ExampleEntity` from the repository.
- Do not put Room annotations on domain-facing models.
- Define synchronization and caching ownership explicitly when remote data exists.
- Do not move every repository implementation into shared core modules automatically.
