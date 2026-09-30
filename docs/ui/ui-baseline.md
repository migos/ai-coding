# UI Baseline

## Description

Every screen must remain understandable and usable with real content, supported window sizes, accessibility services, and system UI.

## Rules

- Put application-owned user-facing strings in resources; support formatting/plurals and localization.
- Map presentation error types to localized messages instead of showing raw exception text.
- Domain/user-provided text is not a string-resource key.
- Expose meaningful accessibility semantics, labels, roles, state, and appropriate descriptions for informative icons/images.
- Decorative content should not produce redundant announcements.
- Prefer standard accessible controls.
- Preserve usable touch targets, focus order, keyboard actions, and visible/enabled states.
- Do not communicate meaning through color alone.
- Verify large font scales, long translations, RTL where supported, and supported narrow/wide/resizable windows.
- Select layout from available window space rather than assuming portrait phone dimensions.
- Handle edge-to-edge system-bar/cutout insets and the IME intentionally.
- Apply each inset at its owning boundary; do not double-pad nested content.
- Required fields/actions must remain reachable with the keyboard open.
- Potentially overflowing content, including errors, forms, and dialogs, must be scrollable.
- Use lazy containers with stable unique keys for large collections; use paging when loading/rendering the full collection is outside the feature's expected limits.
- Preserve required user inputs, list position, and local UI state using the owner-appropriate saved-state mechanism.
- Restore minimal inputs/identifiers and rebuild large data from its source.
- Present loading, empty, failure, retry, and mutation progress states intentionally.
- Keep existing usable data during refresh when the product contract calls for it.
- Respect theme/dark-mode and contrast requirements.
- Avoid device-specific hardcoded sizes for primary layouts.
- Verify applicable accessibility, overflow, inset/IME, and restoration behavior through targeted UI tests or documented manual checks.
- Do not infer usability from a successful build.

## Gotchas

- A scrollable success list does not make the error/form branch scrollable.
- State stored only with `remember` does not survive Activity recreation.
- Stable collection keys must distinguish actual items; duplicate identifiers are a data/identity issue.

## References

- [Accessibility](https://developer.android.com/develop/ui/compose/accessibility/api-defaults)
- [Insets](https://developer.android.com/develop/ui/compose/system/insets)
- [Resources](https://developer.android.com/guide/topics/resources/localization)
