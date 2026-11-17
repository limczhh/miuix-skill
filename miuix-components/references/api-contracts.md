# Verified API Contract Index

This file routes exact Miuix API questions to a narrow contract guide. It is not the contract itself and should not be loaded together with every contract guide.

The maintained snapshot is release tag v0.9.5-beta01 at commit 6d2554a850b8960e2616c968263ff64beb5ffb03. The pinned declaration remains subordinate to the target project's resolved dependency and compilation.

## Select one contract guide

| Need | Read |
|---|---|
| Application theme or Scaffold host/insets | [Theme and page host](contracts/theme-and-host.md) |
| TopAppBar, SmallTopAppBar, TopAppBarLayout, or collapsing behavior | [Top bars and scroll](contracts/top-bars-and-scroll.md) |
| PullToRefresh, rememberPullToRefreshState, overScrollVertical, or overscroll visual bridge | [Scroll, pull-to-refresh, and overscroll](contracts/scroll-refresh-and-overscroll.md) |
| Pager gesture override, native fallback, or BreadcrumbBar list state | [Pager and breadcrumb](contracts/pager-and-breadcrumb.md) |
| Card, SmallTitle, NavigationBar, or FloatingNavigationBar | [Surfaces and navigation](contracts/surfaces-and-navigation.md) |
| Dialogs (Overlay/Window), Bottom sheets (Overlay/Window), List popups, or Snackbar | [Overlays and popups](contracts/overlays.md) |
| Preference rows (Switch/Arrow/Checkbox/Radio/Slider), Dropdown/Spinner preferences, or Dropdown menus | [Preferences and menus](contracts/preferences-and-menus.md) |
| Basic controls (Switch, Checkbox, Radio, Slider), Button, TextField, SearchBar, or NumberPicker | [Inputs and actions](contracts/inputs-and-actions.md) |

## Contract rules

- Read only the matching guide and heading.
- The declaration block records public parameter names, types, order, and defaults for the pinned snapshot.
- The import block identifies the Miuix package; target dependency wiring and standard Compose imports still require checking.
- Docs and Demo explain intent or isolated usage; Example and consumer code explain integration. None replaces the declaration.
- Confirm beta01 resolution and recheck any local source override; another Miuix version is outside the supported scope.

## When the symbol is not listed

Do not enlarge one contract guide for every component. Read [Source verification](source-verification.md), locate the declaration through [Library map](library-map.md), and add a new contract guide only when the API is high-frequency, structurally important, or has repeatedly caused integration errors.

For navigation stack APIs, use the dedicated [miuix-nav guide](miuix-nav.md) and the same source-verification gate.
