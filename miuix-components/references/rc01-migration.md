# `0.9.5-beta03` -> `0.9.5-rc01` Migration Note

This reference records only verified `0.9.5-beta03` (`3526cfe2ca0779c5a2eab99af8737a96c9a8a885`) -> `0.9.5-rc01` (`542463a0863b393a3d6052237daae6c3da217e70`) changes. It is a transition note, not a multi-version compatibility layer; check the active component contract and pinned doc/source before adopting a symbol.

## Toolchain and Dependencies

The dependency version catalog (`gradle/libs.versions.toml`) did not change between `0.9.5-beta03` and `0.9.5-rc01`. Consume the matching **`0.9.5-rc01`** Maven coordinates consistently across modules (`miuix-ui`, `miuix-preference`, `miuix-glass`, `miuix-nav`, `miuix-window`, `miuix-icons`, `miuix-glass-icons`, etc.).

## Breaking & Signature Changes

- **`GlassBottomSheet` body & default padding (`miuix-glass`)**:
  - `content` changed from `@Composable () -> Unit` to **`@Composable (PaddingValues) -> Unit`** (`innerPadding` provides top header clearance when `progressiveBlur = true`; apply `.padding(innerPadding)` to the scrollable body/container).
  - `insideMargin` default changed from `BottomSheetDefaults.insideMargin` to **`GlassBottomSheetDefaults.InsideMargin`** (`DpSize(12.dp, 0.dp)`).
  - Added `progressiveBlur: Boolean = true`, `headerBottomContent: @Composable () -> Unit = {}`, and `scrollOffset: (() -> Float)? = null`.
  - Uses `SheetMotion.Glass` instead of the default full-height sheet slide, extends `GlassIconButton` header inheritance to `startAction` / `endAction` / `headerBottomContent`, and omits the bottom edge stroke on the sheet and its overscroll extension.
- **`miuix-glass-icons` icon renames (`miuix-glass-icons`)**:
  - `MiuixIcons.Glass.XiaominListen` -> **`MiuixIcons.Glass.XiaomiListen`**
  - `MiuixIcons.Glass.CeditSelection` -> **`MiuixIcons.Glass.CheckSelection`**
- **`miuix-preference` popup/dialog `onDismissFinished` parameter reorder**:
  - `OverlayDropdownPopup`, `OverlayDropdownDialog`, `WindowDropdownPopup`, and `WindowDropdownDialog` moved `onDismissFinished` to the end as an optional nullable parameter: `onDismissFinished: (() -> Unit)? = null` (previously a required 4th positional `() -> Unit`). Named call sites continue compiling unchanged; positional call sites must reorder or pass by name.
- **Data-class to regular-class conversions (`miuix-blur`, `miuix-ui`)**:
  - `BlurColors` (`miuix-blur`), `DropdownEntry`, and `DropdownItem` (`miuix-ui`) changed from Kotlin `data class` to regular `@Immutable` classes with explicit `equals`, `hashCode`, `toString`, `copy`, `componentN`, and defensive `.toList()` copies on collection inputs. Normal equality, destructuring, and `copy` remain supported; bytecode or `kotlin-reflect` checks relying on `data class` metadata change.
- **`GlassTopAppBarDefaults.BandOverhang` & `TopAppBar` scroll note**:
  - `GlassTopAppBarDefaults.BandOverhang` changed from `36.dp` to **`32.dp`**.
  - `TopAppBarState.heightOffsetLimit` is now snapshot-backed (`mutableFloatStateOf`); the basic `TopAppBar` guide removed redundant `.overScrollVertical()` when connecting `nestedScroll(scrollBehavior.nestedScrollConnection)`.
- **`OverlayBottomSheet` / `WindowBottomSheet` / `GlassBottomSheet` `title` header reservation**:
  - The sheet header reserves vertical space whenever any of `title`, `startAction`, or `endAction` is non-null (including `title = ""`); the drag handle is unaffected.

## New APIs & Capabilities

- **`GlassCard` (`miuix-glass`)**: New non-clickable and interactive card overloads (`GlassCardDefaults.CornerRadius = 20.dp`, `InsideMargin = CardDefaults.InsideMargin`) for grouping content inside `GlassBottomSheet` and `GlassDialog`. Draws a translucent fill rather than sampling/blurring the backdrop itself; see [Glass notes](contracts/glass.md#glasscard).
- **Route-bound Component Expansion (`miuix-nav`)**:
  - `NavTransitions.Expansion` and `NavTransitions.expansion(containerColor = Color.Unspecified, cornerRadius = 28.dp, motion = NavMotion.Default)`, paired with `Modifier.navTransitionSource(route, cornerRadius = 16.dp, containerColor = Color.Unspecified, corners = NavTransitionCorners(cornerRadius), shape = null, sourceKey = null)`.
  - Page-container expansion only (not multi-element shared content matching). Owns its animated clip (`NavTransition.handlesCornerClip`), causing `NavDisplayEffects.enableCornerClip` to be skipped while preserving `dimAmount`. `NavTransitionScope` adds `entryKey`, `layoutPositionInWindow`, and `windowToLocal(position)`; `NavTransition` adds `transformContent` and `handlesCornerClip`. See [miuix-nav](miuix-nav.md#component-expansion).
- **`DialogMotion` & `SheetMotion` (`miuix-ui`)**:
  - `DialogMotion.Default` / `DialogMotion.Glass` added via `motion: DialogMotion = DialogMotion.Default` on `OverlayDialog` / `WindowDialog`; `SheetMotion.Default` / `SheetMotion.Glass` added via `motion: SheetMotion = SheetMotion.Default` on `OverlayBottomSheet`, `WindowBottomSheet`, and public `BottomSheetOverlayLayout`.
  - Typed Glass surface adapters (`glassDialogSurface` / `glassBottomSheetSurface`) pair with `motion = DialogMotion.Glass` / `SheetMotion.Glass` in the official docs.
- **`GlassPopup` family alignment (`miuix-glass`)**: `GlassPopup`, `GlassTransformPopup`, and `GlassIconDropdownMenu` add `horizontalAlignment: Alignment.Horizontal = Alignment.End`.
- **Desktop / Custom-Window Inset Framing (`miuix-ui`, `miuix-window`)**: New `WindowComponentFrame` and `LocalWindowComponentFrame` in `top.yukonga.miuix.kmp.window` constrain `Window*` overlays, `Tooltip`, and list popups inside custom window client insets and rounded corners; `WindowListPopup` and `WindowCascadingListPopup` always clamp popup height to the viewport safe area after margins even when `maxHeight` is set.
- **Input, Clear Button & Indication Updates (`miuix-ui`, `miuix-glass`)**:
  - `Slider`, `VerticalSlider`, and `RangeSlider` add tap-to-seek gesture support.
  - `Switch` thumb drag uses `folmeSpring` position tracking and improved drag cancellation arbitration.
  - Extracted public `SearchClearButton` (`top.yukonga.miuix.kmp.basic`); `GlassSearchOverlay.closeIcon` now serves solely as the cancel icon beside the input.
  - `MiuixIndication` adds `pressAlpha`, `hoverAlpha`, and `focusAlpha` (wired to theme-aware `MiuixTheme` values), draws before content, and adds a ~200 ms press-release hold (`PRESS_RELEASE_HOLD_MS`).
- **Deferred Edge Strokes, Blur, Material & Squircle Performance**:
  - `Modifier.glass`, `Modifier.glassPanel`, and `Modifier.edgeStroke` add deferred lambda `stroke: () -> EdgeStroke?` overloads.
  - `rememberTiltLightState(base, sensitivity): State<EdgeLightSource>` and `EdgeLightSource.withTilt(...)` (`top.yukonga.miuix.kmp.material`) allow draw-phase tilt tracking without recomposition; `ShaderBrushCache` and cache-accepting draw helpers reduce shader allocations.
  - `miuix-blur` adds `Backdrop.backdropCoordinates`, `BackdropEffectScope.buildRuntimeShaderEffect`, zero-blur bypass, 1D blur specialization, clamped padding, and `GraphicsLayer` reuse.
  - `miuix-squircle` adds per-corner `Path.addSquircleRect(width, height, topLeft, topRight, bottomRight, bottomLeft, ...)`, `ModifierNodeElement` path/shader caching, hardware `CircleShape` capsule fast-paths, and cached `Path` fallbacks for oversized nodes (`> 4096 px` edge or `> 4096 × 2160` area).
