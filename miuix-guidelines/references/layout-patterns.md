# Layout, Top Bar, Gesture, and Content Patterns

Placement and diagnostic patterns for standalone scrolling pages, status-bar background extension, scroll/refresh/overscroll chains, grouped content cards, and mutually exclusive overlays. Verify exact Miuix declarations via `miuix-components`.

Contents: [top bar & status bar](#top-bar-and-status-bar-composition) · [gesture diagnosis](#gesture-dynamics-diagnosis) · [content & overlays](#content-and-overlay-composition)

## Top Bar and Status-Bar Composition

Implements [Standard top app bar](rules/page-shell.md#standard-top-app-bar) and [Insets & status-bar background](rules/page-shell.md#insets-and-content-padding):

```text
page owner: window policy + scroll behavior + backdrop policy + barColor
└── Scaffold
    ├── topBar: full frame (includes owned top safe area)
    │   ├── background: matchParentSize blur / solid surfaceColor
    │   └── foreground: adaptive TopAppBar (safe padding inside)
    └── content: Box(layerBackdrop + surface) -> scroll owner (shared behavior + consumed padding)
```

- Helper names below (`rememberBlurBackdrop`, `BlurredBar`, `AdaptiveTopAppBar`, `pageContentPadding`, `pageScrollModifiers`) illustrate **app-owned** shared wrappers, not extra public Miuix APIs:

```kotlin
val backdrop = rememberBlurBackdrop()
val blurActive = backdrop != null
val barColor = if (blurActive) Color.Transparent else MiuixTheme.colorScheme.surface
val scrollBehavior = MiuixScrollBehavior()
val listState = rememberLazyListState()

Scaffold(
    topBar = {
        BlurredBar(backdrop, blurActive) {
            AdaptiveTopAppBar(
                title = title,
                color = barColor,
                scrollBehavior = scrollBehavior,
                navigationIcon = { IconButton(onClick = onBack) { backIconContent() } },
            )
        }
    },
) { padding ->
    val contentPadding = pageContentPadding(padding, extraBottom = 16.dp)
    Box(
        modifier = (if (backdrop != null) Modifier.layerBackdrop(backdrop) else Modifier)
            .background(MiuixTheme.colorScheme.surface),
    ) {
        LazyColumn(
            state = listState,
            modifier = Modifier
                .fillMaxSize()
                .consumeWindowInsets(contentPadding)
                .pageScrollModifiers(enableScrollEndHaptic = true, showTopAppBar = true, topAppBarScrollBehavior = scrollBehavior),
            contentPadding = contentPadding,
        ) { /* Semantic content */ }
    }
}
```

- **Why background extends behind the status bar**: Keep the outer `BlurredBar` frame unpadded on top so its `matchParentSize` blur/surface paints the status-bar strip too, while foreground bar insets keep titles/buttons clear (`TopAppBarLayout`/`SmallTopAppBarLayout` paint `.background` before their internal top inset; `defaultWindowInsetsPadding = false` leaves the top inset active unless already consumed). For `GlassTopAppBar`, pass foreground clearance via `contentModifier` so its progressive band keeps top-edge origin.
- **Variants**: Hero pinned headers keep one visible `SmallTopAppBar`; step/non-scrolling pages record an exception basis; expanded search follows [Search rules](rules/settings-and-overlays.md#search); explicit Glass shells follow [Glass adoption](glass-adoption.md).

## Gesture Dynamics Diagnosis

Implements [Scroll rules](rules/page-shell.md#scroll-refresh-and-haptics) and [Tabs & pagers](rules/page-shell.md#tabs-and-pagers).

Trace input across participants:
`primary scroll -> app-bar/refresh nestedScroll -> overscroll & spring -> visual consumers -> business refresh`.

```text
Default List-owned:  TopAppBar(behavior)  + PullToRefresh(no behavior) -> list(overscroll -> behavior)
Compat Wrapper-owned: TopAppBar(behavior) + PullToRefresh(behavior)    -> list(no bar connection)
```

| Symptom | Likely root cause | Inspect first |
|---|---|---|
| Bar collapses twice or skips | Duplicate app-bar connection | Wrapper vs inner list modifiers |
| Refresh & bar consume each other | Wrong connection order/owner | Target `PullToRefresh` args vs list chain |
| Header snaps on rebound release | Missing damped/spring frames | Overscroll callback through zero reset |
| Spinner ends, content stays offset | Refresh & overscroll state mixed | `isRefreshing` vs `overscrollOffset` |
| Pager, list & tabs fight | Direction/active-page ownership | Pager & vertical child nested-scroll chains |
| Tab bounces back or jitters on drag | Intent mixed with `currentPage`/`targetPage` | Coordinator lock & `currentPage` sync |
| First/last pager edge slides sideways | Unwanted horizontal overscroll | `HorizontalPager(overscrollEffect = null)` & ancestors |
| Reorder drag closes sheet | Competing drag & sheet dismiss | Sheet `enableNestedScroll` & drag handle |
| Touch works, wheel/trackpad breaks | Input-path-specific handling | CrossAxis/platform scroll setup via `miuix-components` |

## Content and Overlay Composition

Implements [Grouped content cards](rules/design-system.md#grouped-content-cards) and [Settings & overlays](rules/settings-and-overlays.md):

- **Body shape choice**: `SmallTitle` + `Card` (small fixed groups) · Keyed lazy items/cards (`LazyColumn`) · `Column.verticalScroll` (small forms) · `SearchBar` / `GlassSearchOverlay` (expanded search) · `GlassCard` (grouping inside `GlassBottomSheet` / `GlassDialog`; `GlassBottomSheet` passes top `PaddingValues` when `progressiveBlur = true` and `0.dp` when `false`).

```kotlin
item { SmallTitle(text = "Feature Group") }
item {
    Card(modifier = Modifier.fillMaxWidth().padding(horizontal = 12.dp).padding(bottom = 12.dp)) {
        SwitchPreference(checked = enabled, onCheckedChange = { enabled = it }, title = "Enable", summary = "Core service")
        OverlayDropdownPreference(items = modes, selectedIndex = mode, onSelectedIndexChange = { mode = it }, title = "Mode", enabled = enabled)
        SliderPreference(value = level, onValueChange = { level = it }, title = "Level", enabled = enabled)
    }
}
```

- **Mutually exclusive popup state** (`Overlay*` under page `Scaffold` vs separate `Window*` host):

```kotlin
private enum class ActiveOverlay { EditName, DeleteConfirm }
var activeOverlay by remember { mutableStateOf<ActiveOverlay?>(null) }
var nameDraft by remember { mutableStateOf(savedName) }

OverlayDialog(
    show = activeOverlay == ActiveOverlay.EditName,
    title = "Edit name",
    onDismissRequest = { activeOverlay = null },
    onDismissFinished = { nameDraft = savedName }, // Reinitialize cleanly on reopen; guard against reopened session
) {
    Column(verticalArrangement = Arrangement.spacedBy(16.dp)) {
        // Editor content
        Row(horizontalArrangement = Arrangement.spacedBy(20.dp)) {
            TextButton(text = "Cancel", onClick = { activeOverlay = null }, modifier = Modifier.weight(1f))
            TextButton(text = "Confirm", onClick = { onSave(nameDraft); activeOverlay = null }, colors = ButtonDefaults.textButtonColorsPrimary(), modifier = Modifier.weight(1f))
        }
    }
}
```
