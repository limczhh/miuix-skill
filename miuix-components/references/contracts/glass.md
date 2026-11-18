# Glass Component Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: release tag `v0.9.5-beta01` at commit `6d2554a850b8960e2616c968263ff64beb5ffb03`.

Use when the task needs exact signatures, parameters, imports, defaults, or host requirements for `miuix-glass` components, bars, popups, dialogs, sheets, or low-level modifiers.

Before copying a call:
- Confirm target project dependency: `implementation("top.yukonga.miuix.kmp:miuix-glass:0.9.5-beta01")`.
- Shader-backed effects on Android require API 33. Higher-level components provide fallback fills; custom panels must supply their intended fallback. Check [dependency and manifest setup](../setup-and-theme.md#quick-start) separately. Choosing ordinary or Glass presentation remains a product decision.
- **Sampling Boundary**: Always exclude the Glass surface itself, the dialog/sheet scrim, and the popup host from the sampled `Backdrop` (`Modifier.layerBackdrop`) to prevent self-sampling recursive artifacts.

---

Read only the matching section; these declaration blocks describe library APIs, not application reimplementations.

Jump to: [Sampling Primitives](#sampling-primitives) · [Glass TopAppBar](#glass-topappbar) · [Glass Navigation: Bar & Rail](#glass-navigation-bar--rail) · [Glass Tabs: TabRow & SegmentedTabRow](#glass-tabs-tabrow--segmentedtabrow) · [Glass Overlays: Dialogs & BottomSheets](#glass-overlays-dialogs--bottomsheets) · [Glass Preferences & Popups](#glass-preferences--popups) · [Glass Buttons & Actions](#glass-buttons--actions)

## Sampling Primitives

**Module:** `miuix-blur`, `miuix-glass`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.blur.Backdrop
import top.yukonga.miuix.kmp.blur.rememberLayerBackdrop
import top.yukonga.miuix.kmp.blur.layerBackdrop
```

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

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassTopAppBar.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.glass.GlassTopAppBar
import top.yukonga.miuix.kmp.glass.GlassTopAppBarDefaults
import top.yukonga.miuix.kmp.basic.ScrollBehavior
```

### Overload 1: ScrollBehavior-Driven

```kotlin
@Composable
fun GlassTopAppBar(
    title: String,
    modifier: Modifier = Modifier,
    contentModifier: Modifier = Modifier,
    expandable: Boolean = true,
    progressiveBlur: Boolean = false,
    backdrop: Backdrop? = null,
    largeTitle: String = title,
    subtitle: String = "",
    scrollBehavior: ScrollBehavior? = null,
    visualContentOffset: (() -> Float)? = null,
    bandBrush: Brush = GlassTopAppBarDefaults.bandBrush(MiuixTheme.colorScheme.surface),
    bandOverhang: Dp = GlassTopAppBarDefaults.BandOverhang,
    titleBlurRadius: Dp = GlassTopAppBarDefaults.TitleBlurRadius,
    style: GlassStyle = GlassDefaults.Style,
    material: GlassMaterial = GlassTopAppBarDefaults.buttonMaterial(),
    alpha: Float = 1f,
    buttonSize: Dp = GlassTopAppBarDefaults.ButtonSize,
    buttonShape: GlassShape = GlassShape(buttonSize / 2),
    fill: Color = GlassTopAppBarDefaults.buttonFill(),
    stroke: EdgeStroke? = GlassTopAppBarDefaults.buttonStroke(),
    buttonShadow: GlassShadow? = GlassShadows.Regular,
    defaultWindowInsetsPadding: Boolean = true,
    titleAlpha: () -> Float = { 1f },
    backgroundAlpha: () -> Float = { 1f },
    navigationIcon: @Composable () -> Unit = {},
    actions: @Composable RowScope.() -> Unit = {},
    bottomContent: @Composable () -> Unit = {},
)
```

### Overload 2: Explicit `isContentScrolled` State

```kotlin
@Composable
fun GlassTopAppBar(
    title: String,
    isContentScrolled: Boolean,
    modifier: Modifier = Modifier,
    contentModifier: Modifier = Modifier,
    expandable: Boolean = true,
    progressiveBlur: Boolean = false,
    backdrop: Backdrop? = null,
    largeTitle: String = title,
    subtitle: String = "",
    scrollBehavior: ScrollBehavior? = null,
    visualContentOffset: (() -> Float)? = null,
    bandBrush: Brush = GlassTopAppBarDefaults.bandBrush(MiuixTheme.colorScheme.surface),
    bandOverhang: Dp = GlassTopAppBarDefaults.BandOverhang,
    titleBlurRadius: Dp = GlassTopAppBarDefaults.TitleBlurRadius,
    style: GlassStyle = GlassDefaults.Style,
    material: GlassMaterial = GlassTopAppBarDefaults.buttonMaterial(),
    alpha: Float = 1f,
    buttonSize: Dp = GlassTopAppBarDefaults.ButtonSize,
    buttonShape: GlassShape = GlassShape(buttonSize / 2),
    fill: Color = GlassTopAppBarDefaults.buttonFill(),
    stroke: EdgeStroke? = GlassTopAppBarDefaults.buttonStroke(),
    buttonShadow: GlassShadow? = GlassShadows.Regular,
    defaultWindowInsetsPadding: Boolean = true,
    titleAlpha: () -> Float = { 1f },
    backgroundAlpha: () -> Float = { 1f },
    navigationIcon: @Composable () -> Unit = {},
    actions: @Composable RowScope.() -> Unit = {},
    bottomContent: @Composable () -> Unit = {},
)
```

---

## Glass Navigation: Bar & Rail

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassNavigationBar.kt`, `GlassNavigationRail.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.glass.GlassNavigationBar
import top.yukonga.miuix.kmp.glass.GlassNavigationBarDefaults
import top.yukonga.miuix.kmp.glass.GlassNavigationItem
import top.yukonga.miuix.kmp.glass.GlassNavigationRail
import top.yukonga.miuix.kmp.glass.GlassNavigationRailDefaults
import top.yukonga.miuix.kmp.glass.GlassNavigationRailState
import top.yukonga.miuix.kmp.glass.rememberGlassNavigationRailState
```

### GlassNavigationBar

```kotlin
@Composable
fun GlassNavigationBar(
    items: List<GlassNavigationItem>,
    selectedIndex: Int,
    onSelect: (Int) -> Unit,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    defaultWindowInsetsPadding: Boolean = true,
    style: GlassStyle = GlassDefaults.Style,
    shape: GlassShape = GlassShape(CornerSize(50), CornerSize(50), CornerSize(50), CornerSize(50)),
    alpha: Float = 1f,
    visible: Boolean = true,
    stroke: EdgeStroke? = GlassNavigationBarDefaults.stroke(),
    shadow: GlassShadow? = GlassShadows.Float,
    height: Dp = GlassNavigationBarDefaults.Height,
    material: GlassMaterial? = GlassNavigationBarDefaults.material(),
    indicatorColor: Color = GlassNavigationBarDefaults.indicatorColor(),
    indicatorPressedColor: Color = GlassNavigationBarDefaults.indicatorPressedColor(),
    selectedColor: Color = if (MiuixTheme.isDynamicColor) MiuixTheme.colorScheme.primary else MiuixTheme.colorScheme.onBackground,
    unselectedColor: Color = MiuixTheme.colorScheme.onBackground,
    trailingAction: (@Composable () -> Unit)? = null,
)
```

### GlassNavigationRail

```kotlin
@Composable
fun GlassNavigationRail(
    items: List<GlassNavigationItem>,
    selectedIndex: Int,
    onSelect: (Int) -> Unit,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    state: GlassNavigationRailState = rememberGlassNavigationRailState(),
    secondaryItems: List<GlassNavigationItem> = emptyList(),
    showToggle: Boolean = true,
    defaultWindowInsetsPadding: Boolean = true,
    collapsedTop: Dp = GlassNavigationRailDefaults.CollapsedTop,
    expandedWidth: Dp = GlassNavigationRailDefaults.ExpandedWidth,
    style: GlassStyle = GlassDefaults.Style,
    alpha: Float = 1f,
    material: GlassMaterial? = GlassNavigationRailDefaults.material(),
    stroke: EdgeStroke? = GlassNavigationRailDefaults.stroke(),
    shadow: GlassShadow? = GlassShadows.Float,
    selectedColor: Color = if (MiuixTheme.isDynamicColor) MiuixTheme.colorScheme.primary else MiuixTheme.colorScheme.onSurface,
    unselectedColor: Color = MiuixTheme.colorScheme.onSurface,
    indicatorColor: Color = GlassNavigationBarDefaults.indicatorColor(),
    indicatorPressedColor: Color = GlassNavigationBarDefaults.indicatorPressedColor(),
    labelStyle: TextStyle = MiuixTheme.textStyles.body1,
    expandContentDescription: String = GlassNavigationRailDefaults.ExpandContentDescription,
    collapseContentDescription: String = GlassNavigationRailDefaults.CollapseContentDescription,
    scrollState: ScrollState = rememberScrollState(),
)
```
> [!NOTE]
> `GlassNavigationRail` is an overlay surface that morphs between an expanded start-side panel and a collapsed top capsule. Use `state.progress` to coordinate main content layout clearance.

---

## Glass Tabs: TabRow & SegmentedTabRow

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassTabRow.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.glass.GlassTabRow
import top.yukonga.miuix.kmp.glass.GlassSegmentedTabRow
import top.yukonga.miuix.kmp.glass.GlassTabRowDefaults
import top.yukonga.miuix.kmp.glass.GlassSegmentedTabRowDefaults
import top.yukonga.miuix.kmp.glass.GlassTabColors
```

### GlassTabRow

```kotlin
@Composable
fun GlassTabRow(
    tabs: List<String>,
    selectedIndex: Int,
    onSelect: (Int) -> Unit,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    style: GlassStyle = GlassDefaults.Style,
    alpha: Float = 1f,
    surfaceAlpha: Float = 1f,
    stroke: EdgeStroke? = GlassTabRowDefaults.stroke(),
    shadow: GlassShadow? = GlassShadows.Float,
    material: GlassMaterial? = GlassTabRowDefaults.material(),
    height: Dp = GlassTabRowDefaults.Height,
    tabGap: Dp = GlassTabRowDefaults.TabGap,
    colors: GlassTabColors = GlassTabRowDefaults.primaryColors(),
)
```

### GlassSegmentedTabRow

```kotlin
@Composable
fun GlassSegmentedTabRow(
    tabs: List<String>,
    selectedIndex: Int,
    onSelect: (Int) -> Unit,
    backdrop: Backdrop,
    modifier: Modifier = Modifier,
    style: GlassStyle = GlassDefaults.Style,
    alpha: Float = 1f,
    surfaceAlpha: Float = 1f,
    stroke: EdgeStroke? = GlassSegmentedTabRowDefaults.stroke(),
    shadow: GlassShadow? = GlassShadows.Float,
    material: GlassMaterial? = GlassSegmentedTabRowDefaults.material(),
    height: Dp = GlassSegmentedTabRowDefaults.Height,
    trackColor: Color = GlassSegmentedTabRowDefaults.restingTrackColor(),
    indicatorColor: Color = GlassSegmentedTabRowDefaults.indicatorColor(),
    selectedContentColor: Color = MiuixTheme.colorScheme.onSurface,
    contentColor: Color = MiuixTheme.colorScheme.onSurface,
)
```
> [!IMPORTANT]
> When pairing `GlassSegmentedTabRow` with a `HorizontalPager`, always bind `selectedIndex = pagerState.targetPage` (instead of `currentPage`). `GlassSegmentedTabRow` updates its settle generation on pointer release; binding `currentPage` causes indicator rebound animation conflicts.

---

## Glass Overlays: Dialogs & BottomSheets

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassDialog.kt`, `GlassBottomSheet.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.glass.GlassDialog
import top.yukonga.miuix.kmp.glass.GlassDialogDefaults
import top.yukonga.miuix.kmp.glass.GlassDialogButton
import top.yukonga.miuix.kmp.glass.GlassDialogButtonDefaults
import top.yukonga.miuix.kmp.glass.GlassDialogButtonRole
import top.yukonga.miuix.kmp.glass.glassDialogSurface
import top.yukonga.miuix.kmp.glass.GlassBottomSheet
import top.yukonga.miuix.kmp.glass.GlassBottomSheetDefaults
import top.yukonga.miuix.kmp.glass.glassBottomSheetSurface
```

### GlassDialog

```kotlin
@Composable
fun GlassDialog(
    onDismissRequest: () -> Unit,
    show: Boolean,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    enableWindowDim: Boolean = true,
    defaultWindowInsetsPadding: Boolean = true,
    renderInRootScaffold: Boolean = true,
    largeScreen: Boolean? = null,
    title: String? = null,
    titleColor: Color = DialogDefaults.titleColor(),
    summary: String? = null,
    summaryColor: Color = DialogDefaults.summaryColor(),
    cornerRadius: Dp? = null,
    maxWidth: Dp = DialogDefaults.MaxWidth,
    outsideMargin: DpSize = DialogDefaults.outsideMargin,
    insideMargin: DpSize = DialogDefaults.insideMargin,
    material: GlassMaterial = GlassDialogDefaults.material(),
    stroke: EdgeStroke? = GlassDialogDefaults.stroke(),
    containerColor: Color = GlassDialogDefaults.containerColor(),
    dimColor: Color = GlassDialogDefaults.ScrimColor,
    onDismissFinished: (() -> Unit)? = null,
    content: @Composable () -> Unit,
)
```

### GlassDialogButton

```kotlin
@Composable
fun GlassDialogButton(
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    role: GlassDialogButtonRole = GlassDialogButtonRole.Normal,
    cornerRadius: Dp = GlassDialogButtonDefaults.CornerRadius,
    minWidth: Dp = GlassDialogButtonDefaults.MinWidth,
    minHeight: Dp = GlassDialogButtonDefaults.MinHeight,
    colors: ButtonColors = GlassDialogButtonDefaults.buttonColors(role),
    pressedColor: Color = GlassDialogButtonDefaults.pressedColor(),
    insideMargin: PaddingValues = GlassDialogButtonDefaults.InsideMargin,
    interactionSource: MutableInteractionSource? = null,
    content: @Composable RowScope.() -> Unit,
)
```

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

```kotlin
@Composable
fun GlassBottomSheet(
    onDismissRequest: () -> Unit,
    show: Boolean,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    enableWindowDim: Boolean = true,
    defaultWindowInsetsPadding: Boolean = true,
    allowDismiss: Boolean = true,
    enableNestedScroll: Boolean = true,
    renderInRootScaffold: Boolean = true,
    title: String? = null,
    startAction: @Composable (() -> Unit)? = null,
    endAction: @Composable (() -> Unit)? = null,
    cornerRadius: Dp = BottomSheetDefaults.cornerRadius,
    sheetMaxWidth: Dp = BottomSheetDefaults.maxWidth,
    outsideMargin: DpSize = BottomSheetDefaults.outsideMargin,
    insideMargin: DpSize = BottomSheetDefaults.insideMargin,
    dragHandleColor: Color = BottomSheetDefaults.dragHandleColor(),
    material: GlassMaterial = GlassBottomSheetDefaults.material(),
    stroke: EdgeStroke? = GlassBottomSheetDefaults.stroke(),
    containerColor: Color = GlassBottomSheetDefaults.containerColor(),
    dimColor: Color = GlassBottomSheetDefaults.ScrimColor,
    onDismissFinished: (() -> Unit)? = null,
    content: @Composable () -> Unit,
)
```

---

## Glass Preferences & Popups

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.glass.GlassDropdownPreference
import top.yukonga.miuix.kmp.glass.GlassDropdownPopup
import top.yukonga.miuix.kmp.glass.GlassPopup
import top.yukonga.miuix.kmp.glass.GlassPopupItem
import top.yukonga.miuix.kmp.glass.GlassIconDropdownMenu
import top.yukonga.miuix.kmp.glass.GlassTransformPopup
import top.yukonga.miuix.kmp.glass.GlassPopupAnchor
import top.yukonga.miuix.kmp.glass.rememberGlassPopupAnchor
import top.yukonga.miuix.kmp.glass.glassPopupAnchor
```

### GlassDropdownPreference (Scaffold-Hosted)

```kotlin
@Composable
fun GlassDropdownPreference(
    onSelectedIndexChange: (Int) -> Unit,
    items: List<String>,
    selectedIndex: Int,
    title: String,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    showValue: Boolean = true,
    renderInRootScaffold: Boolean = true,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    startAction: @Composable (() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    sizing: GlassPopupSizing = GlassPopupSizing(),
    visuals: GlassPopupVisuals = GlassPopupDefaults.visuals(),
    onExpandedChange: ((Boolean) -> Unit)? = null,
)
```

### BoxScope Glass Popup Components

These components are composed inside the caller's root `BoxScope` sharing coordinates with their anchor:

```kotlin
@Composable
fun BoxScope.GlassDropdownPopup(
    show: Boolean,
    onDismissRequest: () -> Unit,
    anchorBounds: Rect,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    anchor: GlassPopupAnchor? = null,
    sizing: GlassPopupSizing = GlassPopupSizing(),
    visuals: GlassPopupVisuals = GlassPopupDefaults.visuals(),
    cornerRadius: Dp = GlassDropdownDefaults.CornerRadius,
    contentPadding: PaddingValues = PaddingValues(vertical = GlassPopupDefaults.ContentPaddingVertical),
    content: @Composable ColumnScope.() -> Unit,
)

@Composable
fun BoxScope.GlassPopup(
    show: Boolean,
    onDismissRequest: () -> Unit,
    anchorBounds: Rect,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    sizing: GlassPopupSizing = GlassPopupSizing(),
    visuals: GlassPopupVisuals = GlassPopupDefaults.visuals(),
    cornerRadius: Dp = GlassPopupDefaults.CornerRadius,
    secondary: Boolean = false,
    contentPadding: PaddingValues = PaddingValues(vertical = GlassPopupDefaults.ContentPaddingVertical),
    content: @Composable ColumnScope.() -> Unit,
)

@Composable
fun BoxScope.GlassIconDropdownMenu(
    show: Boolean,
    onDismissRequest: () -> Unit,
    anchor: GlassPopupAnchor,
    anchorContent: @Composable () -> Unit,
    entries: List<DropdownEntry>,
    backdrop: Backdrop?,
    modifier: Modifier = Modifier,
    secondaryBackdrop: Backdrop? = backdrop,
    sizing: GlassPopupSizing = GlassPopupSizing(),
    visuals: GlassPopupVisuals = GlassPopupDefaults.visuals(),
    collapseOnSelection: Boolean = entries.size <= 1,
)

@Composable
fun BoxScope.GlassTransformPopup(
    show: Boolean,
    onDismissRequest: () -> Unit,
    anchor: GlassPopupAnchor,
    backdrop: Backdrop?,
    anchorContent: @Composable () -> Unit,
    modifier: Modifier = Modifier,
    simplified: Boolean = false,
    stacked: Boolean = false,
    sizing: GlassPopupSizing = GlassPopupSizing(),
    visuals: GlassPopupVisuals = GlassPopupDefaults.visuals(),
    anchorAlpha: Float = 1f,
    maskColor: Color = GlassPopupDefaults.maskColor(),
    cornerRadius: Dp = GlassPopupDefaults.CornerRadius,
    gap: Dp = 0.dp,
    contentPadding: PaddingValues = PaddingValues(vertical = GlassPopupDefaults.ContentPaddingVertical),
    onMeasured: ((Size) -> Unit)? = null,
    content: @Composable ColumnScope.() -> Unit,
)
```

---

## Glass Buttons & Actions

**Module:** `miuix-glass`  
**Source:** `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassIconButton.kt`, `GlassFloatingActionButton.kt`, `GlassFloatingToolbar.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.glass.GlassIconButton
import top.yukonga.miuix.kmp.glass.GlassFloatingActionButton
import top.yukonga.miuix.kmp.glass.GlassFloatingToolbar
import top.yukonga.miuix.kmp.glass.glassPanel
import top.yukonga.miuix.kmp.glass.glass
```

### GlassIconButton

```kotlin
@Composable
fun GlassIconButton(
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    inheritTopBarMaterial: Boolean = true,
    backdrop: Backdrop? = null,
    surfaceAlpha: Float = 1f,
    style: GlassStyle = LocalGlassTopAppBarContext.current?.style ?: GlassDefaults.Style,
    material: GlassMaterial? = null,
    size: Dp = LocalGlassTopAppBarContext.current?.size ?: GlassTopAppBarDefaults.ButtonSize,
    shape: GlassShape = LocalGlassTopAppBarContext.current?.shape ?: GlassShape(size / 2),
    fill: Color = LocalGlassTopAppBarContext.current?.fill ?: GlassTopAppBarDefaults.buttonFill(),
    stroke: EdgeStroke? = LocalGlassTopAppBarContext.current.let { if (it != null) it.stroke else GlassTopAppBarDefaults.buttonStroke() },
    shadow: GlassShadow? = LocalGlassTopAppBarContext.current.let { if (it != null) it.shadow else GlassShadows.Regular },
    content: @Composable () -> Unit,
)
```
> [!WARNING]
> `GlassIconButton` in `0.9.5-beta01` has NO `enabled` parameter. Disabling must be composed at the interaction level (or retain an ordinary `IconButton`).

### GlassFloatingActionButton & GlassFloatingToolbar

Both components include a bloom rim through their default non-null `stroke`; it is separate from `shadow`. `GlassFloatingActionButtonDefaults.stroke()` delegates to `GlassTopAppBarDefaults.buttonStroke()` (theme-selected `GlassEdgeStrokes.SmallLight` / `SmallDark`); the toolbar uses its own `GlassFloatingToolbarDefaults.stroke()`. Set `stroke = null` to omit the rim; do not add a second border for the stock appearance.

The FAB defaults to a primary-colored material/fallback fill. A null backdrop selects the fill path, which still draws the rim when runtime shaders are supported. `drawEdgeStroke` skips drawing without runtime shader support. `surfaceAlpha` affects the surface/stroke; it is not an effects-off switch. The [migration guide](../../../miuix-guidelines/references/glass-migration.md#migrate-floating-actions-and-their-bloom-stroke) owns placement and replacement decisions.

```kotlin
@Composable
fun GlassFloatingActionButton(
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    backdrop: Backdrop? = null,
    surfaceAlpha: Float = 1f,
    style: GlassStyle = GlassDefaults.Style,
    material: GlassMaterial = GlassFloatingActionButtonDefaults.material(),
    size: Dp = GlassFloatingActionButtonDefaults.Size,
    shape: GlassShape = GlassShape(size / 2),
    fill: Color = GlassFloatingActionButtonDefaults.fill(),
    stroke: EdgeStroke? = GlassFloatingActionButtonDefaults.stroke(),
    shadow: GlassShadow? = GlassShadows.Regular,
    content: @Composable () -> Unit,
)

@Composable
fun GlassFloatingToolbar(
    modifier: Modifier = Modifier,
    backdrop: Backdrop? = null,
    cornerRadius: Dp = GlassFloatingToolbarDefaults.CornerRadius,
    shape: GlassShape = remember(cornerRadius) { GlassShape(cornerRadius) },
    outSidePadding: PaddingValues = GlassFloatingToolbarDefaults.OutSidePadding,
    surfaceAlpha: Float = 1f,
    style: GlassStyle = GlassDefaults.Style,
    material: GlassMaterial = GlassFloatingToolbarDefaults.material(),
    fill: Color = GlassFloatingToolbarDefaults.fill(),
    stroke: EdgeStroke? = GlassFloatingToolbarDefaults.stroke(),
    shadow: GlassShadow? = GlassShadows.Regular,
    content: @Composable () -> Unit,
)
```

### Low-level Glass Panel Modifiers

```kotlin
// Modifier with fallback fill when Backdrop? is null or unsupported:
Modifier.glassPanel(
    backdrop = backdrop,
    shape = GlassShape(24.dp),
    fallback = Modifier.clip(shape).background(MiuixTheme.colorScheme.surface),
)

// Modifier requiring non-null Backdrop (no fallback):
Modifier.glass(
    backdrop = backdrop,
    shape = GlassShape(24.dp),
)
```
