# Persistence

## Description

Select storage by durability, structure, access patterns, atomicity, and expected volume. Saved UI state supports restoration, not durable application storage. The code defines the model shared by the repository and UI examples.

## Rules

- Do not use `SharedPreferences` for new persistence implementations.
- Do not serialize collections of structured application records into a single preference value.
- Use DataStore for preference-like data.
- Use Room for structured data that behaves like records.
- Use file storage for large binary content and store metadata separately when required.
- Persistent application data must not rely on `remember`, `rememberSaveable`, or `SavedStateHandle`.
- Saved state is intended for restoration, not durable persistence.
- Choose storage before implementing repository mutations.
- The model reference uses Simple-profile packages; adapt packages and imports together for Modular.
- Prefer persistence mechanisms that provide the atomicity and transaction semantics required by the data.
- Do not introduce custom synchronization merely to compensate for an unsuitable persistence mechanism.
- Persistence decisions must remain valid as the amount of stored data grows within the expected scope of the feature.

## Selection

| Data characteristic | Preferred mechanism |
|---|---|
| Small settings, preferences, flags, and configuration | DataStore |
| Structured application records | Room |
| Collections requiring create, update, delete, sort, filter, or query operations | Room |
| Relational data | Room |
| Structured offline data or cache | Room |
| Small UI-owned state that should survive recreation | `rememberSaveable` |
| Small ViewModel-owned state that should survive recreation | `SavedStateHandle` |
| Large files, images, documents, or binary content | File storage |

## Placement

### Simple profile

```text
app/src/main/java/<base-package>/feature/example/model/
└── Example.kt
```

### Modular profile

```text
core/model/src/main/java/<base-package>/core/model/example/
└── Example.kt
```

## Code

### `Example.kt`

```kotlin
package com.example.app.feature.example.model

data class Example(
    val id: String,
    val value: String,
)
```

## Gotchas

- `SharedPreferences` must not be treated as a lightweight database.
- Serializing an entire collection into one stored value turns individual mutations into whole-dataset read-modify-write operations.
- Moving synchronous storage work to `Dispatchers.IO` addresses thread usage, not persistence semantics or atomicity.
- Adding a `Mutex` can serialize custom mutations but does not make an unsuitable storage mechanism appropriate.
- DataStore is intended for preference-like data, not record-oriented application storage.
- `SavedStateHandle` and `rememberSaveable` must not be used as durable application storage.
- Do not choose persistence based on how little code an implementation requires.
- Re-evaluate the persistence choice before adding increasingly complex workarounds around it.

## References

- [Repository](repository.md)
- [Room DAO](room-dao.md)
- [ViewModel](../ui/viewmodel.md)
