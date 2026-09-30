# Dependency Injection

## Description

Dependency injection makes construction, ownership, and lifetime explicit. It does not change architectural dependency direction.

## Rules

- Prefer constructor injection for repositories, data sources, business logic, and ViewModels.
- Record manual DI or a selected framework in project decisions; reuse the established choice.
- A DI framework is not mandatory.
- Build the graph at an application composition root and module-owned bindings/factories.
- Consumers receive dependencies rather than looking them up from a global container.
- Keep repository implementations internal when module boundaries support it.
- In the Modular profile, core-owned interfaces live in `:core:data`, models in `:core:model`, and implementation construction stays inside the owning module.
- The app assembles public bindings/factories; feature UI consumes contracts.
- Own Room databases, shared clients, and DataStore instances at the appropriate application/account lifetime.
- Never keep multiple active DataStore instances for one file in one process.
- Configure supported multiprocess access deliberately when required.
- A longer-lived component must not retain an Activity, View, destination, or shorter-lived scope.
- Use application context for infrastructure that requires a context; supply Activity-bound integrations at their lifecycle boundary.
- Create screen ViewModels using the destination's `ViewModelStoreOwner` and factory creation extras.
- Do not use plain constructors in composable bodies or rely on Activity scope by accident.
- Supply `SavedStateHandle` through the owner-aware factory.
- Pass Navigation 3 route arguments explicitly; do not assume they appear in the handle automatically.
- Scope mutable per-account data deliberately and isolate/clear it on logout or account changes.
- An application singleton is not automatically correct for account-owned data.
- Give manually owned coroutine scopes an explicit owner, cancellation/cleanup policy, and failure handling.
- Tests may supply fakes and test dispatchers through the same construction seams.
- Avoid separate production service-locator paths just to support tests.

## Gotchas

- Repeated database/client construction in screens wastes resources and creates inconsistent lifetimes.
- The one-instance-per-file DataStore rule applies even when two repositories independently create what appears to be the same configuration.

## References

- [Navigation](../navigation/navigation.md)
- [ViewModel](../ui/viewmodel.md)
- [DI](https://developer.android.com/training/dependency-injection)
- [DataStore](https://developer.android.com/topic/libraries/architecture/datastore)
- [ViewModel factories](https://developer.android.com/topic/libraries/architecture/viewmodel/viewmodel-factories)
