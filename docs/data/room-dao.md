# Room DAO

## Description

DAOs own Room queries and mutations inside the database boundary. The complete references are consumed by the repository and registered by the database.

## Rules

- DAOs expose Room entities, not domain-facing models.
- Observable queries return `Flow` when live updates are required.
- One-shot database operations use `suspend`.
- Use `@Transaction` when multiple database operations must be atomic.
- Keep SQL readable and narrowly scoped.
- DAOs must not be accessed directly from ViewModels or composables.
- Adapt Simple-profile packages and imports together when using Modular placement.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/data/
├── ExampleDao.kt
└── ExampleEntity.kt
```

### Modular profile

```text
core/database/src/main/java/<base-package>/core/database/example/
├── ExampleDao.kt
└── ExampleEntity.kt
```

## Code

### `ExampleEntity.kt`

```kotlin
package com.example.app.feature.example.data

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "examples")
data class ExampleEntity(
    @PrimaryKey val id: String,
    val value: String,
)
```

### `ExampleDao.kt`

```kotlin
package com.example.app.feature.example.data

import androidx.room.Dao
import androidx.room.Query
import androidx.room.Transaction
import androidx.room.Upsert
import kotlinx.coroutines.flow.Flow

@Dao
interface ExampleDao {
    @Query("SELECT * FROM examples ORDER BY value, id")
    fun observeAll(): Flow<List<ExampleEntity>>

    @Query("SELECT * FROM examples ORDER BY value, id")
    suspend fun getAll(): List<ExampleEntity>

    @Upsert
    suspend fun upsert(entity: ExampleEntity)

    @Upsert
    suspend fun upsertAll(entities: List<ExampleEntity>)

    @Query("DELETE FROM examples WHERE id = :itemId")
    suspend fun deleteById(itemId: String)

    @Query("DELETE FROM examples")
    suspend fun deleteAll()

    @Transaction
    suspend fun replaceAll(entities: List<ExampleEntity>) {
        deleteAll()
        upsertAll(entities)
    }
}
```

## Gotchas

- Never return domain-facing models directly from a DAO.
- Never access a DAO directly from a ViewModel.
- `@Transaction` guarantees database atomicity, not coroutine dispatcher behavior.
- Broad observable queries can cause unnecessary invalidations.

## References

- [Repository](repository.md)
- [Room Database](room-database.md)
