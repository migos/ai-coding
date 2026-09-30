# Project Exceptions

## Description

Record rare, scoped deviations from active standards. The code block is an example entry, not an active exception.

## Rules

- Every exception must state its scope.
- Every exception must include a reason.
- Prefer temporary exceptions with a cleanup condition when possible.
- Do not use exceptions to legitimize accidental inconsistency.
- An exception overrides only the stated rule and scope.

## Code

```md
## Legacy integration

Scope:

`feature/<feature-name>`

Exception:

The presentation layer accesses a platform-bound SDK directly.

Reason:

The SDK requires Activity callbacks and cannot currently be isolated cleanly.

Cleanup condition:

Remove this exception when the integration is replaced.
```

## Gotchas

- Exceptions without scope become accidental global rules.
- Do not create an exception before checking whether the normal architecture can support the requirement.
- Review and remove obsolete exceptions.

## References

- [Project decisions](decisions.md)
- [Project setup](setup.md)
