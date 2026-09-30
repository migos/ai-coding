# Room Database

## Description

The Room database owns database configuration, entity registration, DAO exposure, and schema versioning.

Feature-specific queries belong in DAOs rather than the database class.

## Rules

- Keep the database class small.
- Register persisted entities explicitly.
- Expose DAOs through abstract functions.
- Increase the schema version when the schema changes.
- Provide explicit production migrations.
- Avoid destructive migration as the default production strategy.
- Do not expose the database directly to UI code.

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/database/
└── AppDatabase.kt
```

### Modular profile

```text
core/database/src/main/java/<base-package>/core/database/
└── AppDatabase.kt
```

## Code

### `AppDatabase.kt`

```kotlin
@Database(
    entities = [
        ExampleEntity::class,
    ],
    version = 1,
    exportSchema = true,
)
abstract class AppDatabase : RoomDatabase() {

    abstract fun exampleDao(): ExampleDao
}
```

## Gotchas

- Do not put business logic into the database class.
- Do not expose `AppDatabase` directly to ViewModels.
- Schema changes require migration handling.
- Destructive migration can cause user-data loss.
