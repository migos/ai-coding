# Room DAO

## Description

A DAO defines access to persisted Room data.

DAOs are implementation details of the database boundary.

## Rules

- DAOs expose Room entities, not domain-facing models.
- Observable queries return `Flow` when live updates are required.
- One-shot database operations use `suspend`.
- Use `@Transaction` when multiple database operations must be atomic.
- Keep SQL readable and narrowly scoped.
- DAOs must not be accessed directly from ViewModels or composables.

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
@Entity(
    tableName = "examples",
)
data class ExampleEntity(
    @PrimaryKey
    val id: String,
    val value: String,
)
```

### `ExampleDao.kt`

```kotlin
@Dao
interface ExampleDao {

    @Query(
        """
        SELECT *
        FROM examples
        ORDER BY value
        """
    )
    fun observeAll(): Flow<List<ExampleEntity>>

    @Query(
        """
        SELECT *
        FROM examples
        ORDER BY value
        """
    )
    suspend fun getAll(): List<ExampleEntity>

    @Upsert
    suspend fun upsertAll(
        entities: List<ExampleEntity>,
    )

    @Query("DELETE FROM examples")
    suspend fun deleteAll()

    @Transaction
    suspend fun replaceAll(
        entities: List<ExampleEntity>,
    ) {
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
