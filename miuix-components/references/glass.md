# Glass Components

Use this reference only when the user explicitly asks for glass, frosted, translucent, backdrop-sampled, or liquid-surface UI, or when the target already uses `miuix-glass`. It describes the optional Glass family; it does not replace the ordinary `miuix-ui` component route and does not make glass mandatory for a Miuix page.

The current catalog evidence is Miuix `v0.9.5-beta01` at `6d2554a850b8960e2616c968263ff64beb5ffb03`. Calls below are catalog-verified against that tag; target resolution and compilation still have to be checked separately. For replacing old components or keeping both families, use the dedicated [Glass migration guide](../../miuix-guidelines/references/glass-migration.md); dependency and platform setup is in [Project setup](setup-and-theme.md).

## Module boundary

| Module | Role | Target boundary |
|---|---|---|
| `miuix-glass` | Glass surfaces and composed controls | Depends on `miuix-ui`, `miuix-material`, and `miuix-blur` |
| `miuix-material` | `GlassStyle`, `GlassMaterial`, `GlassShape`, `GlassShadow`, edge strokes, runtime shader/material primitives | Add directly only when application code uses these types or lower-level modifiers |
| `miuix-blur` | `Backdrop`, layer recording, blur and progressive blur modifiers | Android blur artifact requires API 33; verify the target's manifest/minSdk boundary |
| `miuix-glass-icons` | Generated `MiuixIcons.Glass` vectors | Independent optional icon artifact |

For dependency setup and Android fallback behavior, read the current upstream [Glass Surfaces guide](https://github.com/compose-miuix-ui/miuix/blob/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/guide/glass.md). For material presets, read [Material](https://github.com/compose-miuix-ui/miuix/blob/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/guide/material.md).

## Import boundaries

The composed Glass components, anchors, defaults, and modifiers are exported from `top.yukonga.miuix.kmp.glass`. The backdrop primitives come from `top.yukonga.miuix.kmp.blur` (`Backdrop`, `rememberLayerBackdrop`, `layerBackdrop`), the material definitions come from `top.yukonga.miuix.kmp.material`, and `DropdownEntry`/`DropdownItem` come from `top.yukonga.miuix.kmp.basic`. Do not infer a Glass symbol from the similarly named `miuix-ui` package; confirm the declaration's package in the pinned source.

## Background sampling contract

Glass is not selected by passing a translucent color alone. The common surface contract is:

1. Create one `Backdrop` with `rememberLayerBackdrop()`.
2. Apply `Modifier.layerBackdrop(backdrop)` to the opaque page/background content that should be sampled.
3. Place the Glass surface outside that recorded content and pass the same `backdrop` to it.
4. Exclude the Glass surface, popup host, and dialog/sheet scrim from the recording. Add an opaque background inside the recorded layer; parent backgrounds are not captured automatically.
5. Keep the fallback path visible. A `null` backdrop or unsupported runtime must still produce a readable opaque surface.

Glass surfaces and popup components expose a `backdrop` boundary. `GlassDialog`, `GlassBottomSheet`, and `GlassDropdownPreference` are Scaffold-hosted and use the Miuix `MiuixPopupHost`; keep them composed until their exit animation finishes. `GlassPopup`, `GlassDropdownPopup`, `GlassTransformPopup`, `GlassSecondaryPopup`, and `GlassIconDropdownMenu` are `BoxScope` components instead: compose them in the same root `BoxScope` as their anchor, keep their window-coordinate anchor alive, and let the caller own `show`/selection state.

## Component routes

| User need | Component family | Current documentation | Source route |
|---|---|---|---|
| Collapsing glass title bar with actions | `GlassTopAppBar` | `docs/components/glass/glasstopappbar.md` | `miuix-glass/.../glass/GlassTopAppBar.kt` |
| Floating glass destination navigation | `GlassNavigationBar` | `docs/components/glass/glassnavigationbar.md` | `miuix-glass/.../glass/GlassNavigationBar.kt` |
| Morphing wide-window navigation overlay | `GlassNavigationRail` | `docs/components/glass/glassnavigationrail.md` | `miuix-glass/.../glass/GlassNavigationRail.kt` |
| Glass tabs in a bar or page | `GlassTabRow`, `GlassSegmentedTabRow` | `docs/components/glass/glasstabrow.md` | `miuix-glass/.../glass/GlassTabRow.kt` |
| Round glass action or toolbar control | `GlassIconButton` | `docs/components/glass/glassiconbutton.md` | `miuix-glass/.../glass/GlassIconButton.kt` |
| Floating primary action / action group | `GlassFloatingActionButton`, `GlassFloatingToolbar` | `docs/components/glass/glassfloatingactionbutton.md`, `glassfloatingtoolbar.md` | `GlassFloatingActionButton.kt`, `GlassFloatingToolbar.kt` |
| Anchored menu or selected popup item | `GlassPopup`, `GlassPopupItem`, `GlassDropdownPopup` | `docs/components/glass/glasspopup.md`, `glassdropdownpopup.md` | `GlassPopup.kt`, `GlassDropdownPopup.kt` |
| Glass icon menu with cascading child choices | `GlassIconDropdownMenu` | No dedicated component page; integrated Example use | `miuix-glass/.../glass/GlassIconDropdownMenu.kt` |
| Morphing control into a popup / child popup | `GlassTransformPopup`, `GlassSecondaryPopup` | `docs/components/glass/glasstransformpopup.md`, `glasssecondarypopup.md` | `GlassTransformPopup.kt`, `GlassSecondaryPopup.kt` |
| Glass confirmation/input surface | `GlassDialog`, `GlassDialogButton` | `docs/components/glass/glassdialog.md` | `GlassDialog.kt`, `GlassDialogButton.kt` |
| Glass sheet | `GlassBottomSheet` | `docs/components/glass/glassbottomsheet.md` | `GlassBottomSheet.kt` |
| Glass setting row with a value dropdown | `GlassDropdownPreference` | `docs/components/glass/glassdropdownpreference.md` | `GlassDropdownPreference.kt` |

These docs are selection and usage evidence. If the target needs an exact parameter or the source has changed, inspect the declaration rather than copying a partial table.

## Lower-level Glass surfaces and support types

These public APIs are not page slots, but they are the source route when the task asks for a custom Glass panel or an existing Miuix surface slot:

| Need | Public API | Source route |
|---|---|---|
| Custom panel with opaque fallback | `Modifier.glassPanel(backdrop, shape, ...)` | `miuix-glass/.../glass/GlassPanel.kt` |
| Custom sampled surface without a shadow | `Modifier.glass(backdrop, shape, ...)` | `miuix-glass/.../glass/Glass.kt` |
| Glass-only shadow | `Modifier.glassShadow(shape, ...)` | `miuix-glass/.../glass/Glass.kt` |
| Adapt a popup surface to a host-provided `Shape` | `glassSurface(backdrop, visuals, ...)` | `miuix-glass/.../glass/GlassSurface.kt` |
| Adapt a dialog or sheet surface to its animated host | `glassDialogSurface(...)`, `glassBottomSheetSurface(...)` | `GlassDialogSurface.kt`, `GlassBottomSheetSurface.kt` |
| Reuse a control as a transform-popup anchor | `GlassPopupAnchor`, `rememberGlassPopupAnchor`, `glassPopupAnchor*` | `miuix-glass/.../glass/GlassTransformPopup.kt` |
| Configure popup size and appearance | `GlassPopupSizing`, `GlassPopupVisuals` | `miuix-glass/.../glass/GlassPopupSurface.kt` |
| Reuse the library's pressed-surface scale | `Modifier.glassPress(...)`, `glassPressScale(...)` | `miuix-glass/.../glass/GlassPress.kt` |

The important boundary is `glassPanel` versus `glass`: `glassPanel` accepts `Backdrop?` and can use `fallback`; `glass` requires a non-null `Backdrop` and does not provide the panel fallback. Verify the full declaration before adding material, stroke, shadow, or shading parameters.

`glassPanel` without a supplied fallback only clips when sampling is absent/unsupported; it does not invent an opaque fill. Higher-level components provide their own fill policy. `Modifier.glass(enabled = false)` bypasses the effect; setting `alpha = 0f` does not disable blur. Release-summary names `GlassPanel` and `GlassSurface` are not uppercase composables exported by this tag.

Minimal custom-panel shape:

```kotlin
val shape = GlassShape(24.dp)

Box(
    modifier = Modifier.glassPanel(
        backdrop = backdrop,
        shape = shape,
        material = GlassMaterials.puredThinGlass(isDark),
        stroke = GlassEdgeStrokes.forTheme(isDark),
        fallback = Modifier
            .clip(shape)
            .background(MiuixTheme.colorScheme.surface),
    ),
) {
    // Foreground content remains sharp.
}
```

This is a catalog-verified call shape, not a requirement to replace a higher-level Glass component with a custom modifier.

## BoxScope popup boundary

The `BoxScope` popup family does not register with `MiuixPopupHost`. It renders in the caller's root `BoxScope`, so the anchor, popup, and recorded page scene must share the coordinate space. Use `rememberGlassPopupAnchor()` and apply `Modifier.glassPopupAnchor(anchor, cornerRadius = ...)` to the outer control. Apply `glassPopupAnchorContent(anchor)` to the copied icon content when using `GlassTransformPopup` or `GlassIconDropdownMenu`; keep the popup composed during its exit animation.

`GlassIconDropdownMenu` uses the common `DropdownEntry`/`DropdownItem` model from `miuix-ui`. Its required parameters are `show`, `onDismissRequest`, `anchor`, `anchorContent`, `entries`, and `backdrop`; selection and item callbacks live in the `DropdownItem` values. `secondaryBackdrop` is for the cascading child scene and defaults to the supplied `backdrop`.

## Verified shell call shapes

The following calls are catalog-verified shapes for the current snapshot. They still require the target dependency, imports, and compilation check.

### Glass top bar + recorded scrolling content

`GlassTopAppBar` has two overloads. The overload below uses the explicit overlap state; the alternative derives it from `scrollBehavior`/`visualContentOffset`.

```kotlin
val backdrop = rememberLayerBackdrop()
val listState = rememberLazyListState()
val scrollBehavior = MiuixScrollBehavior()

Scaffold(
    topBar = {
        GlassTopAppBar(
            title = "Library",
            isContentScrolled = listState.canScrollBackward,
            backdrop = backdrop,
            scrollBehavior = scrollBehavior,
            navigationIcon = {
                GlassIconButton(onClick = onBack) {
                    Icon(backIcon, contentDescription = "Back")
                }
            },
        )
    },
) { padding ->
    LazyColumn(
        state = listState,
        modifier = Modifier
            .fillMaxSize()
            .nestedScroll(scrollBehavior.nestedScrollConnection)
            .layerBackdrop(backdrop)
            .background(MiuixTheme.colorScheme.surface),
        contentPadding = padding,
    ) {
        // Page content.
    }
}
```

`GlassTopAppBar` uses the same `MiuixScrollBehavior` contract as the ordinary `TopAppBar`: the scrolling owner must attach its `nestedScrollConnection`. It supports `expandable = false` for a fixed compact title; its `titleBlurRadius` controls title transition blur, independently of backdrop effects. `progressiveBlur = true` is an explicit band effect choice and requires a supported non-null backdrop; do not turn it on merely because the component is Glass. For rebound-driven material/band visuals, bridge `overScrollVertical(onOffsetChanged = ...)` to `state.overscrollOffset` using the [scroll contract](contracts/scroll-refresh-and-overscroll.md).

Inside this bar, `GlassIconButton` inherits the bar's backdrop/material/geometry by default (`inheritTopBarMaterial = true`). Standalone icon buttons need their intended backdrop supplied explicitly. The beta01 public icon-button signature has no `enabled` argument; do not copy every ordinary `IconButton` parameter into it.

### Glass navigation bar

`GlassNavigationBar` owns no route or back stack. Hoist `selectedIndex` and `onSelect` to the shell owner, and place the bar in that owner's `Scaffold(bottomBar = ...)` or equivalent shell slot.

```kotlin
val items = listOf(
    GlassNavigationItem(homeIcon, label = "Home"),
    GlassNavigationItem(libraryIcon, label = "Library"),
)

GlassNavigationBar(
    items = items,
    selectedIndex = selectedIndex,
    onSelect = onDestinationSelected,
    backdrop = backdrop,
)
```

The current declaration requires `items`, `selectedIndex`, `onSelect`, and `backdrop` (which may be `null` to use an opaque fill). Keep the navigation bar and any trailing action outside the backdrop recording layer.

### Glass navigation rail

`GlassNavigationRail` uses the same four required inputs as `GlassNavigationBar` and the same `GlassNavigationItem` model. Its separate `GlassNavigationRailState` is created with `rememberGlassNavigationRailState(initialExpanded = true)`. `expanded` is writable; `progress` is the animated morph position to read during layout/drawing. This state is not an ordinary `NavigationRailState` alias.

The rail fills its overlay space, draws an expanded start-side panel, and morphs into a top capsule when collapsed. Place it outside recorded content and coordinate page clearance and top-title overlap from the same rail progress. A width-constrained ordinary rail slot is not a drop-in host. `secondaryItems` appear below a divider while expanded; their callback/selection indices follow the primary items (`items.size + secondaryIndex`). Preserve accessibility of secondary destinations in collapsed/compact layouts. `showToggle`, toggle content descriptions, `scrollState`, badges, and safe-inset handling remain part of the concrete source contract.

### Glass floating actions and tabs

```kotlin
GlassFloatingActionButton(onClick = onAction, backdrop = backdrop) {
    actionIconContent()
}
GlassFloatingToolbar(backdrop = backdrop) {
    actionGroupContent()
}
```

These calls belong in the existing `Scaffold` floating slots. Both default `backdrop` to null and use `surfaceAlpha` for material opacity. The toolbar's parameter spelling is `outSidePadding`; do not infer ordinary parameter names.

`GlassTabRow` and `GlassSegmentedTabRow` take `tabs`, `selectedIndex`, `onSelect`, and `backdrop`. **Only GlassTabRow accepts a nullable backdrop**; the segmented variant requires `Backdrop`, including in a product with an effects-off branch. Branch to an appropriate alternate tab presentation when sampling is disabled. The segmented row supports horizontal drag and a morphing indicator; both remain caller-controlled selection surfaces, not pager state owners.

### Glass icon dropdown menu

This is the beta01 cascading menu shape. Unlike `GlassDropdownPreference`, it is not a preference row and does not require a `Scaffold` popup host:

```kotlin
val menuAnchor = rememberGlassPopupAnchor()

Box(Modifier.fillMaxSize()) {
    GlassIconButton(
        onClick = { showMenu = !showMenu },
        modifier = Modifier.glassPopupAnchor(
            anchor = menuAnchor,
            cornerRadius = GlassTopAppBarDefaults.ButtonSize / 2,
            floating = true,
        ),
    ) {
        Icon(
            imageVector = menuIcon,
            contentDescription = "More",
            modifier = Modifier
                .size(28.dp)
                .glassPopupAnchorContent(menuAnchor),
        )
    }

    GlassIconDropdownMenu(
        show = showMenu,
        onDismissRequest = { showMenu = false },
        anchor = menuAnchor,
        anchorContent = { Icon(menuIcon, contentDescription = null) },
        entries = entries,
        backdrop = backdrop,
    )
}
```

`entries` is a `List<DropdownEntry>`; child choices are `DropdownItem(children = ...)`, and item selection is caller-controlled through `DropdownItem.onClick`. The popup must be a sibling of the recorded page layer, not a child of that layer, or it will sample itself.

### Glass overlays and setting dropdowns

These calls are controlled by the caller and must remain composed during exit:

```kotlin
GlassDialog(
    onDismissRequest = { showDialog = false },
    show = showDialog,
    backdrop = backdrop,
) {
    // Content and GlassDialogButton actions.
}

GlassDropdownPreference(
    title = "Log level",
    items = items,
    selectedIndex = selectedIndex,
    onSelectedIndexChange = { selectedIndex = it },
    backdrop = backdrop,
)

GlassBottomSheet(
    onDismissRequest = { showSheet = false },
    show = showSheet,
    backdrop = backdrop,
    title = "More options",
) {
    // Sheet content and actions.
}
```

`GlassDialog`, `GlassBottomSheet`, and `GlassDropdownPreference` are `Scaffold`-hosted popup surfaces. `backdrop` must contain the page behind the surface but not the popup host or scrim. `GlassDropdownPreference` manages expansion internally; the caller owns the selected index. Use ordinary `OverlayDialog`/`OverlayBottomSheet`/`OverlayDropdownPreference` when the product does not require the Glass visual treatment.

For a smaller existing-Overlay migration, `glassDialogSurface(backdrop)` returns an `@Composable DialogSurfaceScope.() -> Modifier` and `glassBottomSheetSurface(backdrop)` returns the corresponding `BottomSheetSurfaceScope` receiver. They compensate animated scrim/coordinates; dialog additionally receives scale, sheet receives `isOverscroll` and suppresses edge stroke on the overscroll extension. These typed helpers differ from `glassSurface(backdrop, visuals)`, whose return type is `@Composable (Shape) -> Modifier` for compatible cascading-popup surface overloads. Window dialogs/sheets do not expose these surface slots in beta01.

## Do not infer

- `GlassNavigationBar` is not a replacement for route ownership or a general floating action.
- A `Backdrop` is not a color token and cannot be replaced with `Color.Transparent`.
- `miuix-glass` is not the same as `miuix-blur`: the former composes surfaces and controls; the latter provides the backdrop/effect primitives.
- `Glass` names are not aliases for ordinary Miuix names. Verify the module and package `top.yukonga.miuix.kmp.glass`.
- A glass component's `*Defaults` values are not page-level spacing rules. Use them only for the component contract.

When the task is about page ownership, shell placement, or the relationship between the recorded content and the bars, hand that part to `miuix-guidelines` and keep this reference for the Glass API/host contract.
