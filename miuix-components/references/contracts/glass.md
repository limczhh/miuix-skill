# Glass Component Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta01 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

Before adopting `miuix-glass`, check [dependency/platform setup](../setup-and-theme.md#quick-start) and the [sampling boundary](../glass.md#background-sampling-contract). Exclude the Glass surface, scrim and popup host from the recorded scene. Component-specific [blur-off behavior](../blur-behavior.md#component-behavior) remains distinct from shader/platform fallback.

## Sampling Primitives

**Module:** `miuix-blur`, `miuix-glass`  
```kotlin
val backdrop = rememberLayerBackdrop()

// Sampled opaque content:
LazyColumn(
    modifier = Modifier
        .fillMaxSize()
        .layerBackdrop(backdrop)
        .background(MiuixTheme.colorScheme.surface)
) { ... }
```

---

## Glass TopAppBar

`modifier` positions the whole band/foreground layout; `contentModifier` applies to the foreground. Keep status-area background rendering outside foreground safe padding. `defaultWindowInsetsPadding = false` does not disable the inner layout's top inset; see [top inset and background behavior](top-bars-and-scroll.md#top-inset-and-background-behavior).

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassTopAppBar.kt`

### Overload 1: ScrollBehavior-Driven

Read the pinned documentation/source: [GlassTopAppBar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasstopappbar.md).

### Overload 2: Explicit `isContentScrolled` State

Read the pinned documentation/source: [GlassTopAppBar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasstopappbar.md).

---

## Glass Navigation: Bar & Rail

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassNavigationBar.kt`, `GlassNavigationRail.kt`

### GlassNavigationBar

`backdrop = null` disables background sampling; `material = null` does not. The `visible` animation separately blurs the whole navigation group and is not disabled by a null backdrop. See [foreground blur](../blur-behavior.md#foreground-blur-is-a-separate-control).

Read the pinned documentation/source: [GlassNavigationBar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassnavigationbar.md).

### GlassNavigationRail

Read the pinned documentation/source: [GlassNavigationRail](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassnavigationrail.md).
> [!NOTE]
> `GlassNavigationRail` is an overlay surface that morphs between an expanded start-side panel and a collapsed top capsule. Use `state.progress` to coordinate main content layout clearance.

---

## Glass Tabs: TabRow & SegmentedTabRow

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassTabRow.kt`

### GlassTabRow

Read the pinned documentation/source: [GlassTabRow](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasstabrow.md).

### GlassSegmentedTabRow

For effects-off behavior use `surfaceAlpha = 0f`, retaining the required non-null backdrop and resting track/indicator. An unrecorded backdrop does not automatically select fallback. See [tab material control](../blur-behavior.md#disable-tab-material-explicitly) for the supported-runtime branch and inherited-context boundary.

Read the pinned documentation/source: [GlassSegmentedTabRow](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasstabrow.md).
> [!IMPORTANT]
> `GlassSegmentedTabRow` settles its draggable indicator toward the supplied selection on release. A pending application request needs a synchronously held visual target so an old pager position cannot pull the indicator backward; use the same coordinator across ordinary/Glass variants. See [tab/pager feedback timing](pager-and-breadcrumb.md#pager) and the [application coordination pattern](../../../miuix-guidelines/references/navigation-compositions.md#tab-selection-and-page-switching).

---

## Glass Overlays: Dialogs & BottomSheets

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassDialog.kt`, `GlassBottomSheet.kt`

### GlassDialog

Read the pinned documentation/source: [GlassDialog](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassdialog.md).

### GlassDialogButton

Read the pinned documentation/source: [GlassDialogButton](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassdialog.md).

### Typed Surface Adapters for Standard Overlays

```kotlin
// Apply to OverlayDialog(surface = ...):
glassDialogSurface(
    backdrop = backdrop,
    material = GlassDialogDefaults.material(),
    stroke = GlassDialogDefaults.stroke(),
    containerColor = GlassDialogDefaults.containerColor(),
)

// Apply to OverlayBottomSheet(surface = ...):
glassBottomSheetSurface(
    backdrop = backdrop,
    material = GlassBottomSheetDefaults.material(),
    stroke = GlassBottomSheetDefaults.stroke(),
    containerColor = GlassBottomSheetDefaults.containerColor(),
)
```

### GlassBottomSheet

Read the pinned documentation/source: [GlassBottomSheet](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassbottomsheet.md).

---

## Glass Preferences & Popups

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/`

### GlassDropdownPreference (Scaffold-Hosted)

Read the pinned documentation/source: [GlassDropdownPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassdropdownpreference.md).

### BoxScope Glass Popup Components

These components are composed inside the caller's root `BoxScope` sharing coordinates with their anchor:

Read the pinned documentation/source: [BoxScope.GlassDropdownPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassdropdownpopup.md), [BoxScope.GlassPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasspopup.md), [BoxScope.GlassIconDropdownMenu](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassIconDropdownMenu.kt), [BoxScope.GlassTransformPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasstransformpopup.md).

---

### GlassSecondaryPopup

**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassSecondaryPopup.kt`

Read the pinned documentation/source: [BoxScope.GlassSecondaryPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasssecondarypopup.md).

This is a caller-controlled child menu in the same root `BoxScope`. Capture its trigger row's resting `boundsInWindow()` before changing the primary transform popup's `stacked` state. Its backdrop includes the primary popup but excludes the child itself; null falls back to the material anchor's backdrop when available. Share `materialAnchor` when both levels should reuse the primary appearance. Keep it composed through collapse; `onDismissFinished` handles completed-exit cleanup.

### GlassPopupItem

**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassPopup.kt`

Read the pinned documentation/source: [GlassPopupItem](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glasspopup.md).

This renders a menu row, including disabled/selected state, optional icon/summary and submenu arrow. It does not create a popup, sample a backdrop, or supply a Glass container. The caller owns selection and the action; the surrounding popup owns material and visibility.

---

## Glass Buttons & Actions

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassIconButton.kt`, `GlassFloatingActionButton.kt`, `GlassFloatingToolbar.kt`

### GlassIconButton

Read the pinned documentation/source: [GlassIconButton](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassiconbutton.md).
> [!WARNING]
> `GlassIconButton` in `0.9.5-beta01` has NO `enabled` parameter. Disabling must be composed at the interaction level (or retain an ordinary `IconButton`).

### GlassFloatingActionButton & GlassFloatingToolbar

Both components include a bloom rim through their default non-null `stroke`; it is separate from `shadow`. `GlassFloatingActionButtonDefaults.stroke()` delegates to `GlassTopAppBarDefaults.buttonStroke()` (theme-selected `GlassEdgeStrokes.SmallLight` / `SmallDark`); the toolbar uses its own `GlassFloatingToolbarDefaults.stroke()`. Set `stroke = null` to omit the rim; do not add a second border for the stock appearance.

The FAB defaults to a primary-colored material/fallback fill. A null backdrop selects the fill path, which still draws the rim when runtime shaders are supported. `drawEdgeStroke` skips drawing without runtime shader support. `surfaceAlpha` affects the surface/stroke; it is not an effects-off switch. The [migration guide](../../../miuix-guidelines/references/glass-migration.md#migrate-floating-action-components) owns placement and replacement decisions.

Read the pinned documentation/source: [GlassFloatingActionButton](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassfloatingactionbutton.md), [GlassFloatingToolbar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/glass/glassfloatingtoolbar.md).

### Low-level Glass Panel Modifiers

Read the modifier declarations through the pinned guide and source. Surface adapters, press helpers and other APIs follow their [source routes](../glass.md#lower-level-glass-surfaces-and-support-types).

Import both extensions from `top.yukonga.miuix.kmp.glass`, `Backdrop` from `top.yukonga.miuix.kmp.blur`, and the Glass/edge-stroke types from `top.yukonga.miuix.kmp.material`. `BlendMode` is `androidx.compose.ui.graphics.BlendMode`.

#### Modifier.glassPanel

**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassPanel.kt`

Read the pinned documentation/source: [Modifier.glassPanel](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/guide/glass.md).

Null backdrop or unsupported shaders select `fallback`; omitting it only clips to `shape`, without supplying an opaque fill. Shadow and supported stroke drawing remain separate from the fill.

#### Modifier.glass

**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/Glass.kt`

Read the pinned documentation/source: [Modifier.glass](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/guide/glass.md).

This requires a non-null backdrop. `enabled = false` or unsupported shaders bypass the pipeline; neither supplies a fallback fill. `material = null` leaves the sampled body path active, and zero opacity is not the disabled branch. See [blur behavior](../blur-behavior.md#component-behavior).

#### Call shapes

The caller supplies `sampling: Backdrop?` after resolving effect policy, and `recordedBackdrop: Backdrop` for the non-null variant. Record the intended scene separately; these calls do not record it. Reuse the same shape for material and fallback clipping:

```kotlin
val panelShape = GlassShape(24.dp)

// Modifier with fallback fill when Backdrop? is null or unsupported:
Modifier.glassPanel(
    backdrop = sampling,
    shape = panelShape,
    fallback = Modifier.clip(panelShape).background(MiuixTheme.colorScheme.surface),
)

// Modifier requiring non-null Backdrop (no fallback):
Modifier.glass(
    backdrop = recordedBackdrop,
    shape = panelShape,
)
```
