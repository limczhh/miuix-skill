---
name: miuix-components
description: "Use for Miuix 0.9.5-rc01 component selection and implementation: resolve components, modules, colors, hosts, Blur/Glass/Squircle/Nav/Window contracts, and verify pinned official docs or source. Use miuix-guidelines for page skeleton, layout rules, and ownership."
---

# Miuix Components

## Scope and Version Pin

- **Pinned release**: **Miuix `0.9.5-rc01`**, tag **`v0.9.5-rc01`**, commit **`542463a0863b393a3d6052237daae6c3da217e70`**.
- Do not maintain multi-version branches or change target dependencies without explicit authorization; report a different resolved version as out of scope.
- Compose/page architecture, spacing profiles, and visual rules belong to `miuix-guidelines`.

## Documentation-First Resolution

Actively read the selected component's official Markdown at the pinned commit before writing non-trivial calls (do not merely paste links). Resolve any repository path `<path>` (`docs/...` or `miuix-.../...`) via:
- Remote raw URL: `https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/<path>`
- Local checkout: `git show v0.9.5-rc01:<path>` (or `git show 542463a0863b393a3d6052237daae6c3da217e70:<path>`)

Official Markdown owns introductions, imports, full parameter tables, and basic examples. Local references under `references/` retain **integration boundaries, defaults, host/lifecycle pitfalls, fallbacks, and upgrade deltas**. Read only the reference file matching the task.

## Direct Routes

| Need | Open directly |
|---|---|
| Library modules, semantic lookup, full component/doc/source table, low-level exports | [Catalog](references/catalog.md) |
| Dependencies, Android API 24/33 & compileSdk 37.2, `MiuixTheme`, `Scaffold`, semantic colors & token lookup | [Setup, theme & colors](references/setup-and-theme.md) |
| Buttons, `TextField`, `Slider`, `Switch`, `SearchBar`, Preference rows, Dropdown/Spinner/Menus | [Controls & menus](references/controls-and-menus.md) |
| `TopAppBar`, `PullToRefresh`, `overScroll*`, `ScrollBar`, `TabRow`, `Pager`, `BreadcrumbBar`, `Card`, `NavigationBar` | [Bars, scroll & navigation](references/bars-scroll-and-navigation.md) |
| `Overlay*` vs `Window*`, `DialogMotion`/`SheetMotion`, `ListPopup`, `WindowComponentFrame`, Desktop `miuix-window` | [Overlays & windows](references/overlays-and-windows.md) |
| `miuix-glass` 21 components, `GlassCard`, `GlassBottomSheet`, `Backdrop` recording, Blur controls & fallbacks | [Glass & blur](references/glass-and-blur.md) |
| Custom Blur pipelines, `EdgeStroke`, `rememberTiltLightState`, `Squircle`, Basic/Extended/Glass Icons | [Effects & icons](references/effects-and-icons.md) |
| `miuix-nav` route keys, `NavBackStack`, `NavDisplay`, transitions, `Expansion`, swipe back | [miuix-nav](references/miuix-nav.md) |
| Breaking changes and new APIs from `0.9.5-beta03` -> `0.9.5-rc01` | [RC01 migration](references/rc01-migration.md) |
| Source escalation workflow, Example file map, evidence labels | [Source verification](references/source-verification.md) |

## Delivery Contract

- Confirm the target's resolved `0.9.5-rc01` coordinate, source set, local substitutions, and host once; do not invent Material-style parameters or merge overloads.
- Deliver concrete code with non-obvious dependency, host, inset, and state decisions.
- Distinguish evidence honestly (`catalog-verified`, `target-source-verified`, `compile-verified`, `runtime-verified`, `API verification blocked`); see [Verification labels](references/source-verification.md#verification-labels).
