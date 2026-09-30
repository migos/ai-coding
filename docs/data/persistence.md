# Persistence

## Description

Persistence technology must be selected based on the characteristics and access patterns of the data.

Application size is not a persistence requirement.

Use the simplest persistence mechanism that correctly supports the required durability, structure, update semantics, querying, concurrency, and data volume.

Saved UI state and durable application persistence serve different purposes and must not be treated as interchangeable.

## Rules

Use the following defaults:

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

Additional rules:

- Do not use `SharedPreferences` for new persistence implementations.
- Do not serialize collections of structured application records into a single preference value.
- Use DataStore for preference-like data.
- Use Room for structured data that behaves like records.
- Use file storage for large binary content and store metadata separately when required.
- Persistent application data must not rely on `remember`, `rememberSaveable`, or `SavedStateHandle`.
- Saved state is intended for restoration, not durable persistence.
- Choose the persistence mechanism before implementing repository mutation behavior.
- Prefer persistence mechanisms that provide the atomicity and transaction semantics required by the data.
- Do not introduce custom synchronization merely to compensate for an unsuitable persistence mechanism.
- Persistence decisions must remain valid as the amount of stored data grows within the expected scope of the feature.

## Code

Use this decision process before implementing persistence:

```text
Is the data temporary UI state?
│
├── Yes
│   ├── UI owns the state
│   │   └── rememberSaveable
│   │
│   └── ViewModel owns the state
│       └── SavedStateHandle
│
└── No
    │
    ├── Is it small preference-like data?
    │   └── DataStore
    │
    ├── Is it structured record-based data?
    │   └── Room
    │
    └── Is it large binary/file content?
        └── File storage
```

Preference-like data typically has characteristics such as:

```text
- Small number of values
- Configuration or user preferences
- No relational structure
- No record-level CRUD requirements
- No complex querying requirements
```

Structured record data typically has characteristics such as:

```text
- Multiple records
- Individual record identity
- Create/update/delete behavior
- Sorting or filtering
- Search or querying
- Relationships
- Partial updates
- Transactional requirements
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