# Repository

## Description

Repositories expose application data and hide storage/transport details. The complete reference uses a core-owned repository in Modular and feature-owned packages in Simple; its Room adapter translates Android `SQLiteException`.

## Rules

- Repository APIs expose domain-facing models.
- Persistence entities and network DTOs must not leave the data boundary.
- Prefer `Flow` for observable data.
- Prefer `suspend` for one-shot asynchronous operations.
- Document expected operational failures in the repository contract and translate known infrastructure failures at the boundary.
- Preserve cancellation and do not disguise programming defects as successful or recoverable data.
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
- Keep a feature-local repository contract and implementation together when core ownership adds no benefit.
- Use the documented profile placement for both packages and imports.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/data/
├── ExampleRepository.kt
└── DefaultExampleRepository.kt
```

### Modular profile

```text
core/data/src/main/java/<base-package>/core/data/example/
├── ExampleRepository.kt
└── DefaultExampleRepository.kt
```

## Code

### `ExampleRepository.kt`

```kotlin
package com.example.app.feature.example.data

import com.example.app.feature.example.model.Example
import kotlinx.coroutines.flow.Flow

// Expected storage failures are translated at the data boundary.
class ExampleDataException(cause: Exception) : Exception(cause)

interface ExampleRepository {
    fun observeItems(): Flow<List<Example>>
    suspend fun save(item: Example)
    suspend fun delete(itemId: String)
}
```

### `DefaultExampleRepository.kt`

```kotlin
package com.example.app.feature.example.data

import android.database.sqlite.SQLiteException
import com.example.app.feature.example.model.Example
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.catch
import kotlinx.coroutines.flow.map

internal class DefaultExampleRepository(
    private val exampleDao: ExampleDao,
) : ExampleRepository {
    override fun observeItems(): Flow<List<Example>> =
        exampleDao.observeAll()
            .map { entities ->
                entities.map { entity -> Example(entity.id, entity.value) }
            }
            .catch { failure ->
                if (failure is SQLiteException) throw ExampleDataException(failure)
                throw failure
            }

    override suspend fun save(item: Example) = storageOperation {
        exampleDao.upsert(ExampleEntity(item.id, item.value))
    }

    override suspend fun delete(itemId: String) = storageOperation {
        exampleDao.deleteById(itemId)
    }

    private suspend fun storageOperation(block: suspend () -> Unit) {
        try {
            block()
        } catch (failure: SQLiteException) {
            throw ExampleDataException(failure)
        }
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

## References

- [Persistence](persistence.md)
- [Room DAO](room-dao.md)
