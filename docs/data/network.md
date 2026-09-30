# Network and Synchronization

## Description

Network data sources own transport details. Repositories define how local and remote data become application data. Offline-first is a requirement-dependent choice, not a mandate for every application.

## Rules

- Keep clients, DTOs, transport exceptions, HTTP responses, and authentication mechanics inside the data/network boundary.
- Map expected failures into the repository's documented application-facing contract.
- Set finite, deliberate connect/read/write or call timeouts supported by the chosen client.
- Cancellation must cancel underlying requests when the API supports it.
- Classify connectivity/timeouts, authentication/authorization, validation, not-found, rate-limit, and server failures according to the operation.
- Do not collapse all failures into an empty successful result.
- Retry only retryable failures, with bounded attempts/backoff and applicable server retry instructions.
- Do not repeatedly retry authentication, validation, or other permanent failures without resolving the cause.
- A timeout does not prove that a remote write failed.
- Do not automatically retry non-idempotent writes without a server-supported idempotency/deduplication protocol or reconciliation strategy.
- Reuse the same operation identity across retries.
- Coordinate token refresh so overlapping requests do not start conflicting refreshes.
- Bound retry after refresh and prevent account/credential leakage across account switches.
- When local and remote sources coexist, document the source of truth, freshness/staleness behavior, synchronization ownership, and conflict-resolution policy.
- For offline-first reads, prefer observing local storage and updating it from synchronization.
- Define whether success means local durability, queued work, or confirmed remote completion.
- Do not present a queued write as server-confirmed success.
- Apply related local changes transactionally.
- A Room transaction cannot make a database write and HTTP request atomic; use persisted operation state/reconciliation where cross-system consistency is required.
- Use ordering/version checks when older responses could overwrite newer edits.
- Do not derive writes from stale UI snapshots without validating the mutation contract.
- Model offline/error/loading states intentionally and test partial failures, stale responses, repeated writes, and recovery.
- For persistent sync, follow Background Work.

## Gotchas

- Connectivity indicators cannot guarantee that a particular request will succeed.
- Never log credentials or sensitive request/response bodies; see Security.
- Do not build custom caching or sync queues before checking the required guarantees and the selected client's/platform's facilities.

## References

- [Background Work](../async/background-work.md)
- [Security](../security/security.md)
- [Data layer](https://developer.android.com/topic/architecture/data-layer)
- [Offline-first](https://developer.android.com/topic/architecture/data-layer/offline-first)
