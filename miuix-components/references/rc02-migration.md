# `0.9.5-rc01` → `0.9.5-rc02` Migration Summary

Verified delta from `v0.9.5-rc01` (`542463a0863b393a3d6052237daae6c3da217e70`) to `v0.9.5-rc02` (`e636bd6c07f14d588fe084f0c3a5b57eafe20230`). Bump Miuix module coordinates to `0.9.5-rc02` (`gradle/libs.versions.toml` bumps `agp` from `9.5.2` to `9.5.3`).

## Breaking & Default Changes

- **`GlassBottomSheet` `progressiveBlur` default (`miuix-glass`)** — [Glass & blur](glass-and-blur.md#glass-overlays-and-glasscard):
  - `progressiveBlur: Boolean` default changed from `true` → **`false`**.
  - When `progressiveBlur = false` (default), the header sits above the sheet body and the `innerPadding: PaddingValues` passed into `content` is `0.dp`; explicitly pass `progressiveBlur = true` if you rely on progressive blur header overlay + top `innerPadding` clearance.
- **`rememberListPopupLayoutInfo` became `internal` (`miuix-ui`)** — [Catalog](catalog.md#public-api-boundaries-and-low-level-exports):
  - `miuix-ui/.../basic/ListPopup.kt` changed `rememberListPopupLayoutInfo` from public `fun` to `internal fun` (no longer callable from consumer modules). `ListPopupColumn` and `ListPopupContent` remain public.
- **`GlassTopAppBarDefaults` progressive/band metrics (`miuix-glass`)** — [Glass & blur](glass-and-blur.md#glass-bars-navigation-and-tabs):
  - `BandOverhang` changed from `32.dp` → **`10.dp`**; `ProgressiveBlurRadius` changed from `80.dp` → **`32.dp`** (progressive ramp starts below `bottomContent`, and the non-progressive fade band eliminates top seam artifacts).

## New APIs & Enhancements

- **`GlassScrollableTabRow` & `GlassTabRowDefaults.MinTabWidth` (`miuix-glass`)** — [Glass & blur](glass-and-blur.md#glass-bars-navigation-and-tabs):
  - Horizontally scrolling label-sized pill tabs (`minTabWidth: Dp = GlassTabRowDefaults.MinTabWidth` = `76.dp`, `contentPadding: PaddingValues = PaddingValues(0.dp)`, nullable `Backdrop?`, shared `selectedIndex` / `onSelect` & `surfaceAlpha = 0f` contract).
- **`rememberLayerBackdrop(backgroundColor, onDrawUnderlay)` (`miuix-blur`)** — [Effects & icons](effects-and-icons.md#custom-blur-pipelines):
  - Adds `backgroundColor: Color = Color.Unspecified` for single-pass opaque backdrop fill and `onDrawUnderlay: (DrawScope.() -> Unit)? = null` for blur-only underlay compositing without breaking single-pass layer recording (replaces manual `rememberLayerBackdrop { drawRect(...); drawContent() }` overrides).
- **Non-interactive `ScrollBar` mode (`miuix-ui`, `miuix-preference`, `miuix-glass`)** — [Bars & scroll](bars-scroll-and-navigation.md#scrollbars):
  - `VerticalScrollBar`, `HorizontalScrollBar`, `Modifier.verticalScrollBar`, and `Modifier.horizontalScrollBar` add **`interactive: Boolean = true`** (`false` acts as a non-interactive scroll indicator). Automatically attached (`isFadeEnabled = false, interactive = false`) to `ListPopupColumn`, `OverlayDropdownDialog`, `WindowDropdownDialog`, and scrollable `GlassPopupSurface`.
- **Unified safe-area + host bounds & popup reveal (`miuix-ui`, `miuix-glass`)** — [Overlays & windows](overlays-and-windows.md#list-popups-and-snackbars):
  - `OverlayListPopup`, `WindowListPopup`, `OverlayCascadingListPopup`, `WindowCascadingListPopup`, `GlassPopup` family, and `Tooltip` now intersect `WindowInsets.systemBars.union(WindowInsets.displayCutout)` (+ `LocalWindowComponentFrame.current?.insets`) with host bounds, capping menu height on **both** `Overlay*` and `Window*` list popups; `PopupLayoutPosition` adds `pivotFractionX` / `pivotFractionY` for clamped anchor reveal alignment; `GlassTransformPopup` scales predictive-back close-rebound momentum (`backReboundMomentum`); `DialogContentLayout` bottom inset takes `maxOf(ime, navBar, captionBar)`.
- **Material & Blur fast paths (`miuix-blur`, `miuix-material`, `miuix-glass`)**:
  - `BackdropPipeline.requiresOffscreenCompositing(size, density)` + `GlassShape.requiresContinuousMask(size, density)` avoid offscreen compositing/masking on rectangles, circles, capsules, or `smoothing == 0f`; `miuix-material` exports `createGlassShadowShader()` & `DrawScope.drawGlassShadowForSize(...)` (`Glass` caches shadow layers); progressive blur switches to linear `raw` threshold ramp and `fastCoerce*` hot-path math.

## Upgrading Directly From `0.9.5-beta03`

If jumping over `rc01`, also account for those earlier `rc01` changes:
- `GlassBottomSheet`: `content` takes `(PaddingValues) -> Unit`, `insideMargin` defaults to `GlassBottomSheetDefaults.InsideMargin` (`12.dp, 0.dp`), uses `SheetMotion.Glass`, omits bottom edge stroke.
- `miuix-glass-icons`: `XiaominListen` → `XiaomiListen`, `CeditSelection` → `CheckSelection`; new `GlassCard` (`CornerRadius = 20.dp`) and `GlassPopup`/`GlassTransformPopup`/`GlassIconDropdownMenu` `horizontalAlignment`.
- `miuix-preference` low-level popups/dialogs moved `onDismissFinished` last; `BlurColors`/`DropdownEntry`/`DropdownItem` became regular `@Immutable` classes; dialogs/sheets added `motion: DialogMotion`/`SheetMotion`; `miuix-nav` added `NavTransitions.Expansion` + `Modifier.navTransitionSource`.
