# Room Database

## Description

The database owns configuration, entity registration, DAO exposure, and schema versions. The complete reference uses the Simple-profile packages.

## Rules

- Keep the database class small.
- Register persisted entities explicitly.
- Expose DAOs through abstract functions.
- Increase the schema version when the schema changes.
- Provide deliberate production migrations, using supported manual or automatic migrations as appropriate.
- Configure the schema export directory, commit schemas, register migrations on the database builder, and test supported upgrade paths and preserved data; see Project Setup.
- Avoid destructive migration as the default production strategy.
- Do not expose the database directly to UI code.
- Create and scope the database at the application composition root.

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
package com.example.app.database

import androidx.room.Database
import androidx.room.RoomDatabase
import com.example.app.feature.example.data.ExampleDao
import com.example.app.feature.example.data.ExampleEntity

@Database(
    entities = [ExampleEntity::class],
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

## References

- [Project Setup](../project/setup.md)
- [Dependency Injection](../di/dependency-injection.md)
- [Room DAO](room-dao.md)
