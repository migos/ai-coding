# Security and Privacy

## Description

Apply a small security baseline to every application and additional measures when the feature handles sensitive data, authentication, external inputs, or exported components. Select protection according to the data and threat model, not blanket encryption rules.

## Rules

- Never embed confidential server credentials or cryptographic secrets in source, resources, assets, build configuration, or the APK.
- Obfuscation and environment-variable substitution do not make bundled secrets private.
- Public client identifiers/keys must have the applicable provider restrictions.
- Keep credentials, tokens, personal data, and sensitive request/response content out of logs, analytics, crash reports, URLs, and exception messages.
- Redact by construction; verify release logging behavior.
- Use HTTPS with normal certificate/hostname validation.
- Do not add trust-all clients or global cleartext exceptions.
- Any necessary development-only relaxation must stay out of production variants.
- Store sensitive data privately, minimize retention, and define account/logout cleanup.
- Use Android Keystore-backed protection when required; do not invent cryptographic protocols or assume Room/DataStore encrypts application data automatically.
- Configure cloud backup and device-to-device extraction rules for the supported Android versions.
- Exclude credentials and other data that must not be copied/restored; document intentional inclusion and test required restore behavior.
- Request only required permissions, at the relevant user action.
- Handle denial, revocation, and restricted capabilities without assuming permission state is permanent.
- Export only components that need external access, with appropriate permissions and input validation.
- Validate deep-link/Intent arguments and authorization before accessing records; an ID received from another app is not proof of access.
- Treat external URIs, files, network responses, and WebView content as untrusted.
- Validate size/type/path assumptions, avoid traversal and injection, and use parameterized database queries.
- Keep unnecessary WebView/JavaScript bridges disabled.
- Share files using narrowly scoped content URIs and temporary grants when appropriate.
- Do not expose broad filesystem roots or world-readable copies of sensitive data.
- Before introducing authentication, sensitive storage, external entry points, or a security-sensitive SDK, document required protections and verify current platform/provider requirements.
- Do not perform unrelated security migrations during feature work.
- Test relevant denial, unauthorized access, malicious input, backup/extraction, and release-configuration paths.

## Gotchas

- Build-time secrets that end up in an APK remain extractable.
- Private app storage, encryption, backup rules, and authorization address different threats.
- A valid deep link can still target data that the current account is not allowed to access.

## References

- [Android security practices](https://developer.android.com/privacy-and-security/security-best-practices)
- [Backup security](https://developer.android.com/privacy-and-security/risks/backup-best-practices)
