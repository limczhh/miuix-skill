---
name: miuix-components
description: "Use when implementing, migrating, debugging, or fixing compile errors in Miuix 0.9.5-rc02 Compose Multiplatform, Android, or Desktop UI: resolve modules, exact package imports (`top.yukonga.miuix.kmp.*`), `MiuixTheme` colors/textStyles, `Scaffold` & `Overlay*`/`Window*` hosts, Preference/Dropdown/Menu contracts, Blur/Glass/Squircle/`miuix-nav`/`miuix-window` APIs, and Material 3 to Miuix parameter mismatches (e.g., `TextFieldState`, `Checkbox`, `ButtonDefaults`, `NavigationBarItem`). Use `miuix-guidelines` for page layout rules, spacing metrics, and ownership."
---

# Miuix Components

## Scope and Version Pin

- **Pinned release**: **Miuix `0.9.5-rc02`**, tag **`v0.9.5-rc02`**, commit **`e636bd6c07f14d588fe084f0c3a5b57eafe20230`**.
- Do not maintain multi-version branches or change target dependencies without explicit authorization; report a different resolved version as out of scope.
- Compose/page architecture, spacing profiles, and visual rules belong to `miuix-guidelines`.

## Documentation-First Resolution

Read **only** the local reference file(s) matching the task first — local files under `references/` already hold `0.9.5-rc02`-verified imports, signatures, overloads, defaults, host traps, and fallbacks. **Do NOT re-read all reference files or fetch upstream docs/Kotlin source for APIs already documented locally.** Escalate to upstream `docs/...` or Kotlin source only when a required declaration is missing from `references/` or to diagnose a real compile/behavior mismatch:
- Remote raw URL: `https://raw.githubusercontent.com/compose-miuix-ui/miuix/e636bd6c07f14d588fe084f0c3a5b57eafe20230/<path>`
- Local checkout: `git show v0.9.5-rc02:<path>` (or `git show e636bd6c07f14d588fe084f0c3a5b57eafe20230:<path>`)

Official Markdown owns full exhaustive parameter tables; local `references/` own concise signatures, integration boundaries, defaults, host/lifecycle pitfalls, fallbacks, and upgrade deltas.

## Direct Routes

| Need | Open directly |
|---|---|
| Library modules, semantic lookup, full component/doc/source table, low-level exports | [Catalog](references/catalog.md) |
| Dependencies, Android API 24/33 & compileSdk 37.2, `MiuixTheme`, `Scaffold`, semantic colors & token lookup | [Setup, theme & colors](references/setup-and-theme.md) |
| Buttons, `TextField`, `Slider`, `Switch`, `SearchBar`, Preference rows, Dropdown/Spinner/Menus | [Controls & menus](references/controls-and-menus.md) |
| `TopAppBar`, `PullToRefresh`, `overScroll*`, `scrollEndHaptic`, `ScrollBar`, `TabRow`, `Pager`, `BreadcrumbBar`, `Card`, `NavigationBar` | [Bars, scroll & navigation](references/bars-scroll-and-navigation.md) |
| `Overlay*` vs `Window*`, `DialogMotion`/`SheetMotion`, `ListPopup`, `WindowComponentFrame`, Desktop `miuix-window` | [Overlays & windows](references/overlays-and-windows.md) |
| `miuix-glass` 22 components, `GlassCard`, `GlassBottomSheet`, `Backdrop` recording, Blur controls & fallbacks | [Glass & blur](references/glass-and-blur.md) |
| Custom Blur pipelines, `EdgeStroke`, `rememberTiltLightState`, `Squircle`, Basic/Extended/Glass Icons | [Effects & icons](references/effects-and-icons.md) |
| `miuix-nav` route keys, `NavBackStack`, `NavDisplay`, transitions, `Expansion`, swipe back | [miuix-nav](references/miuix-nav.md) |
| Breaking changes and new APIs for `0.9.5-rc02` (`rc01` -> `rc02`, plus `beta03` jump summary) | [RC02 migration](references/rc02-migration.md) |
| Source escalation workflow, Example file map, evidence labels | [Source verification](references/source-verification.md) |

## High-Frequency Material 3 Traps

Avoid these common compile/runtime mistakes when writing Miuix `0.9.5-rc02`:
- **Theme tokens**: Read `MiuixTheme.textStyles.<role>` (`title1..4`, `headline1..2`, `subtitle`, `main`, `paragraph`, `body1..2`, `button`, `footnote1..2`) and `MiuixTheme.colorScheme.<role>` — there is no `MiuixTheme.typography` or `.colors`.
- **Keep popups composed**: Never wrap `Overlay*`, `Window*`, `GlassDialog`, or `GlassBottomSheet` in `if (show)` — keep them composed with `show: Boolean` so exit animations and `onDismissFinished` complete.
- **Buttons & inputs**: `ButtonDefaults` owns both `Button` and `TextButton` (`buttonColors(color = ...)` / `buttonColorsPrimary()` / `textButtonColors()` / `textButtonColorsPrimary()`, no `TextButtonDefaults`, no `containerColor`). Use `TextField(state = rememberTextFieldState(), label = ...)` (no `placeholder`/`isError`/`supportingText`), `Checkbox(state = ToggleableState(checked), onClick = ...)`, and `Card(cornerRadius, insideMargin)` (no `shape`/`elevation`/`border`).
- **Preferences, menus & nav bars**: Rows use `startAction`/`endActions`/`bottomAction`; `OverlayDropdownMenu`/`OverlayIconDropdownMenu` manage expanded state internally (no `expanded` input parameter); `NavigationBarItem` and `FloatingNavigationBarItem` take `icon: ImageVector` (not `itemIcon`); `TabRow` takes `selectedTabIndex`/`onTabSelected` whereas `GlassTabRow` / `GlassScrollableTabRow` / `GlassSegmentedTabRow` take `selectedIndex`/`onSelect`.

## Delivery Contract

- Confirm the target's resolved `0.9.5-rc02` coordinate, source set, local substitutions, and host once; do not invent Material-style parameters or merge overloads.
- Deliver concrete code with non-obvious dependency, host, inset, and state decisions.
- Distinguish evidence honestly (`catalog-verified`, `target-source-verified`, `compile-verified`, `runtime-verified`, `API verification blocked`); see [Verification labels](references/source-verification.md#verification-labels).
