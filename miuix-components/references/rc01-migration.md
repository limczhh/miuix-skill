# `0.9.5-beta03` → `0.9.5-rc01` Migration Summary

Verified delta from `v0.9.5-beta03` (`3526cfe2ca0779c5a2eab99af8737a96c9a8a885`) to `v0.9.5-rc01` (`542463a0863b393a3d6052237daae6c3da217e70`). Dependency catalog versions (`gradle/libs.versions.toml`) did not change; bump Miuix module coordinates to `0.9.5-rc01`.

## Breaking & Signature Changes

- **`GlassBottomSheet` (`miuix-glass`)** — [Glass & blur](glass-and-blur.md#glass-overlays-and-glasscard):
  - `content` changed from `@Composable () -> Unit` to **`@Composable (PaddingValues) -> Unit`** (provides top header clearance `innerPadding` while `progressiveBlur = true`; apply `.padding(innerPadding)` to scrollable body).
  - `insideMargin` default changed from `BottomSheetDefaults.insideMargin` to **`GlassBottomSheetDefaults.InsideMargin`** (`DpSize(12.dp, 0.dp)`).
  - Uses **`SheetMotion.Glass`**, extends `GlassIconButton` header context inheritance into `startAction` / `endAction` / `headerBottomContent`, adds `progressiveBlur = true` / `headerBottomContent` / `scrollOffset`, and omits bottom edge stroke on sheet & overscroll extension.
- **`miuix-glass-icons` renames** — [Effects & icons](effects-and-icons.md#icons):
  - `MiuixIcons.Glass.XiaominListen` → **`MiuixIcons.Glass.XiaomiListen`**
  - `MiuixIcons.Glass.CeditSelection` → **`MiuixIcons.Glass.CheckSelection`**
- **`miuix-preference` popup/dialog `onDismissFinished` reorder** — [Controls & menus](controls-and-menus.md#preference-and-menu-contracts):
  - `OverlayDropdownPopup`, `OverlayDropdownDialog`, `WindowDropdownPopup`, and `WindowDropdownDialog` moved `onDismissFinished` to the **end** as optional nullable `(() -> Unit)? = null`.
- **Data-class → regular `@Immutable` class** (`miuix-blur`, `miuix-ui`):
  - `BlurColors`, `DropdownEntry`, and `DropdownItem` converted from `data class` to regular classes with explicit `equals`/`hashCode`/`toString`/`copy`/`componentN` and defensive `.toList()` collection copies (source-compatible; affects `data class` bytecode/reflection).
- **`GlassTopAppBarDefaults.BandOverhang` & `TopAppBar`** — [Bars & scroll](bars-scroll-and-navigation.md#top-app-bar-and-scrollbehavior):
  - `BandOverhang` changed from `36.dp` to **`32.dp`**; `TopAppBarState.heightOffsetLimit` is snapshot-backed; `topappbar.md` example drops redundant `.overScrollVertical()`.
- **Sheet `title` header reservation** — [Overlays & windows](overlays-and-windows.md#dialogs-and-bottom-sheets):
  - Header reserves space when any of `title`, `startAction`, `endAction` is non-null (even `title = ""`); drag handle unaffected.

## New APIs & Enhancements

- **`GlassCard` / `GlassCardDefaults`** (`miuix-glass`): static & clickable cards (`CornerRadius = 20.dp`) for grouping inside `GlassBottomSheet`/`GlassDialog` (translucent fill; no backdrop sampling).
- **`miuix-nav` Component Expansion**: `NavTransitions.Expansion` / `expansion(...)`, `Modifier.navTransitionSource(...)`, `NavTransitionCorners`, `NavTransitionScope` (`entryKey`, `layoutPositionInWindow`, `windowToLocal`), `NavTransition.transformContent` & `handlesCornerClip`; see [miuix-nav](miuix-nav.md#transitions-expansion-and-back-gestures).
- **`DialogMotion` / `SheetMotion` (`Default` / `Glass`)**: `motion` parameter on `OverlayDialog`/`WindowDialog` and `OverlayBottomSheet`/`WindowBottomSheet`/public `BottomSheetOverlayLayout`; paired with typed `glassDialogSurface`/`glassBottomSheetSurface`.
- **`GlassPopup`, `GlassTransformPopup`, `GlassIconDropdownMenu`**: add `horizontalAlignment: Alignment.Horizontal = Alignment.End`.
- **`WindowComponentFrame` / `LocalWindowComponentFrame`**: custom desktop/window inset & corner constraints applied by `miuix-window` frames; `WindowListPopup`/`WindowCascadingListPopup` always clamp height to safe area after margins.
- **Controls & Indication**: `Slider`/`VerticalSlider`/`RangeSlider` add tap-to-seek; `Switch` drag uses `folmeSpring` + cancel arbitration; new public `SearchClearButton` (`GlassSearchOverlay.closeIcon` now cancel-only); `MiuixIndication` adds theme-aware `pressAlpha`/`hoverAlpha`/`focusAlpha`, draws before content, holds short taps ~200 ms; dialogs/sheets reset predictive-back state and release touch earlier.
- **Deferred strokes, Blur & Squircle perf**: `stroke: () -> EdgeStroke?` overloads on `Modifier.glass`, `glassPanel`, `edgeStroke`; `rememberTiltLightState` + `EdgeLightSource.withTilt`; `ShaderBrushCache`; `Backdrop.backdropCoordinates`, `BackdropEffectScope.buildRuntimeShaderEffect`, zero-blur bypass, 1D blur, `GraphicsLayer` reuse; per-corner `Path.addSquircleRect`, `ModifierNodeElement` cache, `CircleShape` capsule bypass, and oversized node `Path` fallback (`> 4096 px` edge or `> 4096 × 2160` area).
