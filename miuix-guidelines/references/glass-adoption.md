# Glass Adoption and Migration Guide

Use for new Glass surfaces or migrating/coexisting ordinary and Glass chrome while preserving state, hosts, and fallbacks. Enforce [Blur and Glass rules](rules/page-shell.md#blur-and-glass) and [Popup lifetime rules](rules/settings-and-overlays.md#popup-family-and-lifetime). Verify exact `miuix-glass` / `miuix-blur` / `miuix-material` APIs, current defaults, and component fallback behavior via `miuix-components`.

Contents: [migration scope](#choose-the-migration-scope) · [recording hierarchy](#background-recording-hierarchy) · [replacement map](#ordinary-to-glass-replacement-map) · [top-bar & overlay coexistence](#top-bar-linkage-and-overlay-adapters) · [policy & acceptance](#coexistence-policy-and-acceptance)

## Choose the Migration Scope

| Product intent | Smallest coherent scope |
|---|---|
| Keep ordinary look | Stay on `miuix-ui` / `miuix-preference` (Glass is optional) |
| Give only a dialog/sheet a Glass surface | Keep `OverlayDialog` / `OverlayBottomSheet` host + content; pass typed `surface` + `.Glass` `motion` |
| Upgrade page chrome | Adopt `GlassTopAppBar` / `GlassNavigationBar` / `GlassTabRow` / `GlassFloating*`, keeping route & scroll owners |
| Offer ordinary & Glass user theme modes | Resolve presentation policy above render branches; share all route, pager, scroll, and draft state |

Adding `miuix-glass` pulls blur transitively; Android `minSdk < 33` apps must handle the blur manifest override in `miuix-components`.

## Background Recording Hierarchy

```text
shell or page root
├── opaque recorded page content -> Modifier.layerBackdrop(backdrop)
├── Glass top / bottom / rail layer -> samples backdrop (outside recorded subtree)
└── popup host or root BoxScope popup -> samples scene, excluded from backdrop & scrim
```

- Always paint an opaque base **inside** the recorded layer (prefer `rememberLayerBackdrop(backgroundColor = ...)` so single-pass recording stays intact).
- To blur page bars **under** a dialog, record the page's inner `Scaffold` inside an outer popup-hosting `Scaffold` and mount the dialog on the outer host.
- `GlassSearchOverlay` records its own result list internally — pass result `scrollOffset` and keep its root outside the backdrop through exit.

## Ordinary to Glass Replacement Map

| Ordinary component | Glass candidate | Preserve & adapt notes (check exact params in `miuix-components`) |
|---|---|---|
| `TopAppBar` + blur wrapper | `GlassTopAppBar` | Same `MiuixScrollBehavior` & back/actions; remove ordinary blur wrapper; pass foreground safe insets via `contentModifier`; `expandable = false` for compact wide header |
| `NavigationBar` / `FloatingNavigationBar` | `GlassNavigationBar` | Same coordinator & `selectedIndex`; convert to `GlassNavigationItem`; update bottom spacing policy |
| `NavigationRail` | `GlassNavigationRail` | **Different overlay/morph layout** + `GlassNavigationRailState`: coordinate clearance & title obstruction from `progress`, keep outside recording |
| `TabRow` / `TabRowWithContour` | `GlassTabRow` / `GlassScrollableTabRow` / `GlassSegmentedTabRow` | Share coordinator (`selectedIndex`/`onSelect` on Glass vs `selectedTabIndex`/`onTabSelected` on ordinary); hold intent lock on `GlassSegmentedTabRow`; turn off material via `surfaceAlpha = 0f` (`GlassTabRow` & `GlassScrollableTabRow` allow `Backdrop?`; `GlassSegmentedTabRow` requires non-null `Backdrop`) |
| `IconButton` | `GlassIconButton` | Inherits header context inside `GlassTopAppBar` and `GlassBottomSheet` headers; supply `backdrop` standalone; has NO `enabled` arg (use ordinary `IconButton` or semantic adapter if disabling needed) |
| `TextField` in Glass modal | `GlassTextField` | State-based `TextFieldState`; host keeps focus & validation |
| Full-screen search | `GlassSearchOverlay` | In-page search owner (not a route); owns focus, Back/Escape & progressive header |
| `FloatingActionButton` / `Toolbar` | `GlassFloatingActionButton` / `Toolbar` | Same `Scaffold` slots; omit extra app rim/blur wrappers (`stroke` & `shadow` built in) |
| `OverlayDialog` | `motion = DialogMotion.Glass` + `glassDialogSurface`, or `GlassDialog` | Preserve show/draft/finished lifecycle; button row uses `GlassDialogButton(onClick, role = GlassDialogButtonRole.*)` |
| `OverlayBottomSheet` | `motion = SheetMotion.Glass` + `glassBottomSheetSurface`, or `GlassBottomSheet` | `GlassBottomSheet` defaults `progressiveBlur = false` (header sits above body, `0.dp` `innerPadding`; enable `progressiveBlur = true` to overlay header and consume `innerPadding`), defaults `GlassBottomSheetDefaults.InsideMargin`, and omits bottom edge stroke |
| `WindowDialog` / `WindowBottomSheet` | Keep Window or migrate explicitly | Glass wrappers are `Scaffold`-hosted, **not** Window replacements |
| `Card` in Glass sheet/dialog vs custom panel | `GlassCard` inside `GlassBottomSheet`/`GlassDialog`; `glassPanel` only for custom sampled boxes | `GlassCard` draws translucent fill without sampling backdrop; keep ordinary `Card` on normal pages |
| Anchored/cascading menus & Dropdown row | `glassSurface` + surface overload, or `GlassIconDropdownMenu` / `GlassPopup` / `GlassTransformPopup` / `GlassDropdownPreference` | `BoxScope` popups share root `Box` with `rememberGlassPopupAnchor()` (outside backdrop); `GlassDropdownPreference` is `Scaffold`-hosted for string items |
| Extended icons | Optional `MiuixIcons.Glass` | Verify exact symbol names in `miuix-components` |

## Top-Bar Linkage and Overlay Adapters

- **Header child material sync (`GlassTabRow` / pills in `bottomContent`)**: Match `GlassTopAppBar`'s boolean overlap threshold (`listState.canScrollBackward || (scrollBehavior?.state?.overscrollOffset ?: 0f) < -1f`), animated via `GlassMotion.topBarButtonFloat()`, driving child `surfaceAlpha` without trying to re-invent `visualContentOffset`.
- **Host-preserving Overlay migration** (keep `show = false` composed until `onDismissFinished`):

```kotlin
OverlayDialog(
    show = showDialog,
    onDismissRequest = closeWithoutSaving,
    onDismissFinished = finishDialogSession,
    dimColor = GlassDialogDefaults.ScrimColor,
    motion = if (useGlassSurface) DialogMotion.Glass else DialogMotion.Default,
    surface = if (useGlassSurface) glassDialogSurface(backdrop = sampling) else null,
) { editorContent() }
```

Use `OverlayBottomSheet(motion = SheetMotion.Glass, surface = glassBottomSheetSurface(sampling))` similarly (never pass generic `glassSurface` to Dialog/Sheet slots).

## Coexistence Policy and Acceptance

- Distinguish **presentation family** (ordinary vs Glass) from **effect capability** (runtime shader support + blur setting). Keep one canonical state/route/pager owner above both.
- **Latch active popup sessions**: Do not switch an open popup between Window and Overlay or recreate an active editor mid-session; apply style switches on reopen or after `onDismissFinished`.
- Remove obsolete ordinary blur wrappers once migrated (retain ordinary components only for active coexistence or fallback branches).
- **Verify**: both presentations, blur on/off on shader-capable devices, unsupported-API fallback separately, light/dark/Monet themes, compact/wide/RTL, scroll/rebound, cancel/reopen, and render inspection confirming no self-sampling or double scrim.
