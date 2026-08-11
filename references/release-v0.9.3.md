# v0.9.2 → v0.9.3 Migration Notes

Use [Source verification](source-verification.md) to keep implementation evidence on the released `v0.9.3` tag, and use [Project setup and theme](setup-and-theme.md) for dependency placement and root-theme integration.

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

The following APIs are absent from `v0.9.3` and were introduced afterward: `BreadcrumbBar`, `OverlayDialog` / `WindowDialog` `maxWidth`, and the later `RadioButtonPreference` color and whole-row interaction changes. Do not use them in `v0.9.3` code.
