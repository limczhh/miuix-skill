# v0.9.2 → v0.9.3 Migration Notes

Use [Source verification](source-verification.md) to keep implementation evidence on the released `v0.9.3` tag, and use [Project setup and theme](setup-and-theme.md) for dependency placement and root-theme integration.

This release is not a version-string-only upgrade. Most additions are source-compatible, but several public signatures and display-mode contracts changed. Inspect the affected call sites below before changing the dependency version.

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

## Breaking or source-sensitive changes

| v0.9.2 usage | v0.9.3 contract | Migration action |
|---|---|---|
| `NavigationRail(mode = NavigationRailDisplayMode...)` and `NavigationRailDisplayMode` | `NavigationRail(state = NavigationRailState?)`; `rememberNavigationRailState()` enables expansion, while `state = null` keeps the collapsed rail | Remove the old `mode` argument and enum usage; choose the new state-driven rail behavior |
| `NavigationBarDisplayMode.TextOnly` | The enum keeps `IconAndText`, `IconOnly`, and `IconWithSelectedLabel`; `TextOnly` is removed | Replace text-only navigation with a supported mode or an application-owned layout |
| `SnackbarColors(containerColor, contentColor, actionContentColor, dismissActionContentColor)` | Adds required `actionContainerColor`; `SnackbarDefaults` also changes its surface/action defaults and layout constants | Prefer `SnackbarDefaults.snackbarColors(...)`; update positional or manually constructed `SnackbarColors` calls with the new field |
| Positional `TextButton(...)` calls after the `insideMargin` parameter | Adds `textStyle: TextStyle? = null` before `interactionSource` | Prefer named arguments; check positional calls for shifted arguments |

`NavigationRail` is the largest API change in this release: it is not just an enum rename. The old display-mode API is replaced by an expandable/collapsed state model with new width, scroll, and accessibility-description parameters. The new `Badge`, `Tooltip`, navigation-item badges, and `TextButton.textStyle` are additive features, but their presence does not make the old calls safe.

The release keeps the library Android baseline at `minSdk = 23`; do not copy the later `v0.9.4-rc01` baseline of 24 into a `v0.9.3` migration.

The following APIs are absent from `v0.9.3` and were introduced afterward: `BreadcrumbBar`, `OverlayDialog` / `WindowDialog` `maxWidth`, and the later `RadioButtonPreference` color and whole-row interaction changes. Do not use them in `v0.9.3` code.
