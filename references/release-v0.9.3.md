# v0.9.2 → v0.9.3 Migration Notes

No breaking API changes. Update the version string and you're done:

```kotlin
// before
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.2")
// after
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.3")
```

What's new (no code changes required):
- **New components**: `Tooltip` / `PlainTooltip` / `RichTooltip` / `TooltipBox` and `Badge` / `BadgedBox` in `miuix-ui`
- **Navigation badges**: `NavigationBarItem` and `FloatingNavigationBarItem` now accept `badge: (@Composable () -> Unit)?`
- **Expandable NavigationRail**: pass `rememberNavigationRailState()` to `NavigationRail(state = ...)` to enable expand/collapse; leave `state = null` for the classic collapsed rail
- **TextButton typography**: `TextButton` now has `textStyle: TextStyle? = null`
- **Snackbar redesign**: `SnackbarHostState` can stack multiple snackbars; `SnackbarHost` handles layout-driven enter/exit and optional swipe-to-dismiss
- **PullToRefresh sync fix**: `isRefreshing` is the hoisted source of truth; setting it `true` while idle shows the indicator, setting it `false` completes refresh, and a gesture joins an already-running refresh instead of firing a duplicate
- **Icons**: basic icons now include `Close` and `Sidebar`; extended icons add `Home`; many extended icons were optically aligned

Post-0.9.3 main branch note: current `main` also contains `BreadcrumbBar`, `OverlayDialog` / `WindowDialog` `maxWidth`, and `RadioButtonPreference` color/row-interaction changes. Do not document those as v0.9.3 APIs unless targeting `main` or a later release tag.
