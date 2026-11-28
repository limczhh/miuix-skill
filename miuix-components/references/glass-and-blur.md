# Glass Components and Blur Controls

Use when the user asks for Glass, translucent, frosted, or backdrop-sampled UI, or when diagnosing blur toggles and runtime fallbacks (`0.9.5-rc02`). For custom Blur shaders, TiltLight, Squircle, and Icons, see [Effects & icons](effects-and-icons.md).

Contents: [recording contract](#background-sampling-contract) · [bars, nav & tabs](#glass-bars-navigation-and-tabs) · [actions & cards](#glass-actions-and-cards) · [dialogs, sheets & adapters](#glass-overlays-and-glasscard) · [BoxScope popups](#boxscope-glass-popups) · [search & text field](#glass-search-and-text-input) · [modifiers & adapters](#low-level-surfaces-and-adapters) · [blur & fallback matrix](#blur-controls-and-fallback-matrix)

## Background Sampling Contract

Guide: `docs/guide/glass.md` (`top.yukonga.miuix.kmp.glass`, `Backdrop` / `rememberLayerBackdrop` / `Modifier.layerBackdrop` from `top.yukonga.miuix.kmp.blur`, materials from `top.yukonga.miuix.kmp.material`).

1. Create `val backdrop = rememberLayerBackdrop(backgroundColor = MiuixTheme.colorScheme.surface)` (use `backgroundColor` for a single-pass opaque fill, or `onDrawUnderlay` for a blur-only underlay composite, rather than overriding `onDraw`).
2. Apply `Modifier.layerBackdrop(backdrop)` to that recorded background/content subtree.
3. Place the Glass surface **outside** that recorded layer (sibling or outer layer) and pass `backdrop`.
4. Exclude the Glass surface, dialog/sheet scrim, and `Scaffold` popup host from recording.
5. Preserve readable component-specific fallbacks (translucent/opaque fill, gradient band, resting track); **an unrecorded `Backdrop` is NOT a blur-off switch**.

## Glass Bars, Navigation, and Tabs

- **`GlassTopAppBar`** (`docs/components/glass/glasstopappbar.md`):
  - Two overloads: `ScrollBehavior`-driven (`contentOffset` + negative `overscrollOffset`) or explicit `isContentScrolled` (+ negative overscroll). Neither accepts `visualContentOffset`. `expandable = false` for compact fixed title; `centerObstructionStart` avoids centered overlays (`TitleControlSpacing = 12.dp`).
  - `modifier` sizes outer frame/band; `contentModifier` applies foreground safe insets. Progressive band (`BandOverhang = 10.dp`, `ProgressiveBlurRadius = 32.dp`, ramp starting below `bottomContent`) requires a non-null supported `backdrop`; `titleBlurRadius` (`8.dp` default; `0.dp` to disable) controls title blur independently.
- **`GlassNavigationBar`** (`glassnavigationbar.md`):
  - Takes `items: List<GlassNavigationItem>`, `selectedIndex: Int`, `onSelect: (Int) -> Unit`, `backdrop: Backdrop?` (note `selectedIndex`/`onSelect` rather than ordinary `NavigationBar`'s `RowScope` children).
  - `backdrop = null` disables background sampling, whereas `material = null` does not. Changing `visible` independently blurs the whole bar group (no separate visibility-blur switch). With `defaultWindowInsetsPadding = true`, owns horizontal cutout/nav insets and bottom nav/caption spacing.
- **`GlassNavigationRail`** (`glassnavigationrail.md`):
  - Uses `rememberGlassNavigationRailState(initialExpanded = true)` (`expanded`, `progress`). Fills overlay space outside recorded content and morphs into a top capsule; coordinate page clearance & `centerObstructionStart` from `progress`. `secondaryItems` start at `items.size + secondaryIndex`.
- **`GlassTabRow`, `GlassScrollableTabRow`, & `GlassSegmentedTabRow`** (`glasstabrow.md`):
  - **Param naming**: All three take **`selectedIndex` / `onSelect`** (whereas ordinary `TabRow` takes `selectedTabIndex` / `onTabSelected`).
  - **Disable material on supported runtime**: pass **`surfaceAlpha = 0f`** on any of them to turn off the material pipeline while keeping selection, semantics, and resting visuals. `GlassTabRow` (equal-width pills) and `GlassScrollableTabRow` (label-sized horizontally scrollable pills with `minTabWidth = GlassTabRowDefaults.MinTabWidth` (`76.dp`) and `contentPadding`) accept `Backdrop?` (`null` uses resting fills); `GlassSegmentedTabRow` **requires non-null `Backdrop`** and has a draggable indicator that settles on pointer release (pair with a parent selection intent lock so stale pager positions do not pull it backward).

## Glass Actions and Cards

- **`GlassIconButton`** (`docs/components/glass/glassiconbutton.md`):
  - **NO `enabled` parameter** (use ordinary `IconButton` or compose semantic disable wrapper if needed).
  - Inside `GlassTopAppBar` and `GlassBottomSheet` (`startAction`, `endAction`, `headerBottomContent`), inherits header backdrop/material/geometry by default (`inheritTopBarMaterial = true`). Standalone buttons require an explicit `backdrop`.
- **`GlassFloatingActionButton` & `GlassFloatingToolbar`** (`glassfloatingactionbutton.md`, `glassfloatingtoolbar.md`):
  - Put in `Scaffold` floating slots. FAB defaults `Size = 56.dp`, primary-tinted fallback/material, built-in `stroke`, and press scale; toolbar uses `outSidePadding` spelling. Set `stroke = null` to omit rim (shader-dependent) and `shadow = null` to omit shadow.

## Glass Overlays and GlassCard

`GlassDialog`, `GlassBottomSheet`, and `GlassDropdownPreference` are **`Scaffold`-hosted** (`MiuixPopupHost`); keep them composed until exit finishes and exclude host/scrim from `backdrop`.

- **`GlassDialog` & `GlassDialogButton`** (`docs/components/glass/glassdialog.md`): uses `DialogMotion.Glass`. Dialog actions use `GlassDialogButton(onClick, role = GlassDialogButtonRole.Normal / .Primary / .Destructive) { Text("...", style = MiuixTheme.textStyles.button) }` (it takes a `RowScope` `content` lambda, not `text = "..."`; upstream `GlassDialogDemo` spaces buttons `12.dp` apart).
- **`GlassBottomSheet`** (`glassbottomsheet.md`) **key `rc02` behavior**:
  - **`progressiveBlur: Boolean = false` is the `rc02` default** (changed from `true` in `rc01`). When `progressiveBlur = false`, the header sits above the body and `innerPadding` passed to `content: @Composable (PaddingValues) -> Unit` is **`0.dp`**; set **`progressiveBlur = true`** to overlay the header with a progressive blur band while scrolling and apply `innerPadding` (`.padding(innerPadding)` or `LazyColumn(contentPadding = innerPadding)`).
  - Defaults `insideMargin` to **`GlassBottomSheetDefaults.InsideMargin`** (`DpSize(12.dp, 0.dp)`), uses `SheetMotion.Glass`, supports `headerBottomContent` and `scrollOffset: (() -> Float)? = null`, and **omits the bottom edge stroke** on both sheet and overscroll extension.
  - Header reserves space whenever any of `title`, `startAction`, `endAction` is non-null (even `title = ""`); sheet action `GlassIconButton` inherits `GlassBottomSheetDefaults` (`ButtonSize = 44.dp`, `buttonFill()`, `buttonMaterial()`, `buttonStroke()`).
- **`GlassCard`** (`glasscard.md`, `GlassCard.kt`):
  - Static and clickable/long-press overloads for grouping content inside `GlassBottomSheet` or `GlassDialog` (`GlassCardDefaults.CornerRadius = 20.dp`, `InsideMargin = CardDefaults.InsideMargin`). Draws translucent theme-aware fill (`color()`/`colors()`); does **not** sample or blur the backdrop itself. When a sheet falls back to opaque `background` (`backdrop == null` or `!isRuntimeShaderSupported()`), pass `GlassCardDefaults.colors(color = MiuixTheme.colorScheme.secondaryContainer)` for clear light/dark card contrast.
- **`GlassDropdownPreference`** (`glassdropdownpreference.md`): caller owns `selectedIndex`; component owns popup expansion. `DropdownItem`/`DropdownEntry` come from `.basic`.

```kotlin
GlassBottomSheet(
    onDismissRequest = { showSheet = false },
    show = showSheet,
    backdrop = backdrop,
    progressiveBlur = true, // Defaults to false in rc02 (0.dp innerPadding when false)
    title = "More options",
) { innerPadding ->
    Column(Modifier.verticalScroll(rememberScrollState()).padding(innerPadding)) {
        GlassCard { /* Grouped content */ }
    }
}
```

## BoxScope Glass Popups

`GlassPopup`, `GlassDropdownPopup`, `GlassTransformPopup`, `GlassSecondaryPopup`, and `GlassIconDropdownMenu` are **`BoxScope` components** (NOT `MiuixPopupHost`). Compose them in the same root `BoxScope` as their anchor (outside the recorded layer) and keep composed through exit:
- Docs: `glasspopup.md`, `glassdropdownpopup.md`, `glasstransformpopup.md`, `glasssecondarypopup.md` (examples in `example/shared/.../MainPage.kt` for `GlassIconDropdownMenu`).
- **`horizontalAlignment: Alignment.Horizontal = Alignment.End`** on `GlassPopup`, `GlassTransformPopup`, and `GlassIconDropdownMenu`. Scrollable panels (`GlassPopupSurface`) attach a non-interactive `verticalScrollBar` automatically and clamp `maxHeight`/position against `systemBars + displayCutout + LocalWindowComponentFrame` intersected with host bounds (`GlassPopup`/`GlassDropdownPopup` align reveal pivots to clamped anchors; `GlassTransformPopup` softens predictive-back close-rebound momentum).
- **Anchors & cascading**: `rememberGlassPopupAnchor()` + `Modifier.glassPopupAnchor(anchor, cornerRadius, floating)` + `glassPopupAnchorContent(anchor)` on copied glyph content. For `GlassSecondaryPopup`, capture resting row `boundsInWindow()` before setting primary `stacked = true`; include primary popup in secondary `backdrop` (or omit to inherit via `materialAnchor`), and use `onDismissFinished` for cleanup.

## Glass Search and Text Input

- **`GlassSearchOverlay`** (`glasssearchoverlay.md`): full-screen in-page search (not a route). Records its own result scene internally (`scrollOffset` + optional `scrollMaterialThreshold`). Uses `SearchClearButton` internally to clear query; `closeIcon` is the cancel icon beside input. Keep outside recorded scene through collapse; Back/Escape must close active search state.
- **`GlassTextField`** (`glasstextfield.md`): Glass colors around `TextField`; prefer `GlassTextField(state = TextFieldState(...))` over deprecated `value` overloads. Does not add validation UI or overlay host.

## Low-Level Surfaces and Adapters

- **Overlay adapters**: `glassDialogSurface(backdrop, ...)` (`@Composable DialogSurfaceScope.() -> Modifier`) and `glassBottomSheetSurface(backdrop, ...)` (`BottomSheetSurfaceScope`, omits bottom edge stroke on sheet/overscroll) — pair with `motion = DialogMotion.Glass` / `SheetMotion.Glass` on `OverlayDialog` / `OverlayBottomSheet`. `glassSurface(backdrop, visuals)` returns `@Composable (Shape) -> Modifier` for cascading popup `surface` overloads. `WindowDialog`/`WindowBottomSheet` lack these slots.
- **`Modifier.glassPanel` vs `Modifier.glass`**: `glassPanel` accepts `Backdrop?` and optional `fallback: Modifier?` (omitting `fallback` only clips, no opaque fill); `glass` requires non-null `Backdrop` and uses `enabled = false` without inventing a fill. Both have **deferred `stroke: () -> EdgeStroke?` overloads** (`GlassPanel.kt`, `Glass.kt`) so draw-phase strokes (e.g. `rememberTiltLightState`) avoid recomposition and blur-pipeline rebuilds.
- **Press helper**: `Modifier.glassPress(...)`, `glassPressScale(...)` (`GlassPress.kt`); popup config: `GlassPopupSizing`, `GlassPopupVisuals` (`GlassPopupSurface.kt`).

## Blur Controls and Fallback Matrix

There is **no global library `enableBlur` flag**. Distinguish:
- `material.isRuntimeShaderSupported()` (Android API 33+, gates blur/shader material/bloom strokes) vs `isRenderEffectSupported()` (API 31+, RenderEffect only).
- `blurRadius = 0f` removes radius only, **not** color blends/processing; `alpha = 0f` is **not** a disable switch.

| Component / effect | Sampling enabled | App turns sampling off | Runtime shaders unsupported |
|---|---|---|---|
| Ordinary `TopAppBar`/`SmallTopAppBar`/`NavigationBar`/`FloatingNavigationBar` | Caller wrapper adds `textureBlur` (+ `edgeStroke` on floating) | Remove wrapper & restore `surface`/`surfaceContainer` | Same ordinary solid surface |
| Ordinary `Card`/`IconButton`/`FAB`/`Toolbar`/`Rail`/`TabRow` | No internal sampler | Keep ordinary surface or gate app modifier | Same ordinary surface |
| Ordinary `SearchBar` / `InputField` | `InputField(color = Transparent)` over caller blur | Restore `surfaceContainerHigh` | Same ordinary capsule |
| `textureBlur`, `textureEffect`, `progressiveTextureBlur`, `drawBackdrop` | Sample separately recorded backdrop | `enabled = false`; caller provides fill | Bypassed; no auto opaque fill |
| `GlassTopAppBar` | Action/tab material + optional `progressiveBlur` | `backdrop = null`; gradient `bandBrush` remains | Non-progressive gradient band; title blur separate |
| `GlassNavigationBar` / `GlassNavigationRail` | Sampled material | `backdrop = null` -> `surfaceContainer`; `visible` blur still runs | Panel fill + shadow fallback; no bloom rim |
| `GlassTabRow` / `GlassScrollableTabRow` / `GlassSegmentedTabRow` | Active material + ramp | Pass **`surfaceAlpha = 0f`** (`null` `Backdrop` allowed on `GlassTabRow` and `GlassScrollableTabRow`; required non-null on segmented) -> resting visuals preserved | Material ramp forced to 0; track & fallback shadow remain |
| `GlassDialog`, `GlassBottomSheet`, typed surface adapters | Sample scrim-adjusted page; sheet `progressiveBlur` blurs sheet body | `backdrop = null` -> `containerColor` (`background`); sheet header falls back to fade gradient | Fill fallback; no shader stroke (sheet omits bottom stroke even with shaders on) |
| `GlassPopup` family / `glassSurface` | Resolved scene/anchor material | `visuals.containerColor`; transition blur independent | Container fill; no shader stroke |
| `GlassCard`, `GlassPopupItem`, `GlassDialogButton` | No independent sampler | Enclosing surface fallback | Same colors/interaction |
| `GlassSearchOverlay` | Progressive header + pills over internal result scene | Opaque full-screen search geometry | Query/focus/Back ownership preserved |
| `Modifier.glassPanel` / `Modifier.glass` | Glass pipeline | `glassPanel(null)` -> `fallback` or clip only; `glass(enabled = false)` bypasses | Same |

**Independent foreground `RenderEffect` / transition blurs** (unaffected by `backdrop = null`): `GlassTopAppBar` title blur (`titleBlurRadius = 0.dp` disables), `GlassNavigationBar.visible` group blur (no disable flag), `GlassPopup`/`DropdownPopup`/`TransformPopup` morph/exit blur.

**Inherited sampling chains**: Always gate sampling at the parent/anchor too — `GlassIconButton` inherits header `buttonBackdrop` inside `GlassTopAppBar` and `GlassBottomSheet` (`inheritTopBarMaterial = false` only drops top-bar material preference); both Glass tab rows prefer top-bar context backdrop; `GlassTransformPopup` falls back to `anchor.surface.backdrop`; `GlassSecondaryPopup` falls back to `materialAnchor`.
