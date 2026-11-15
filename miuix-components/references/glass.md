# Glass Components

Use this reference only when the user explicitly asks for glass, frosted, translucent, backdrop-sampled, or liquid-surface UI, or when the target already uses `miuix-glass`. It describes the optional Glass family; it does not replace the ordinary `miuix-ui` component route and does not make glass mandatory for a Miuix page.

The current source evidence is Miuix `main` at `d8efe1872c08c863a8b33e7044dd2a8b43038032`. Exact declarations below are source-verified against that checkout; a published target version still has to be checked separately.

## Module boundary

| Module | Role | Target boundary |
|---|---|---|
| `miuix-glass` | Glass surfaces and composed controls | Depends on `miuix-ui`, `miuix-material`, and `miuix-blur` |
| `miuix-material` | `GlassStyle`, `GlassMaterial`, `GlassShape`, `GlassShadow`, edge strokes, runtime shader/material primitives | Add directly only when application code uses these types or lower-level modifiers |
| `miuix-blur` | `Backdrop`, layer recording, blur and progressive blur modifiers | Android blur artifact requires API 33; verify the target's manifest/minSdk boundary |
| `miuix-glass-icons` | Generated `MiuixIcons.Glass` vectors | Independent optional icon artifact |

For dependency setup and Android fallback behavior, read the current upstream [Glass Surfaces guide](https://github.com/compose-miuix-ui/miuix/blob/d8efe1872c08c863a8b33e7044dd2a8b43038032/docs/guide/glass.md). For material presets, read [Material](https://github.com/compose-miuix-ui/miuix/blob/d8efe1872c08c863a8b33e7044dd2a8b43038032/docs/guide/material.md).

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
| Glass tabs in a bar or page | `GlassTabRow`, `GlassSegmentedTabRow` | `docs/components/glass/glasstabrow.md` | `miuix-glass/.../glass/GlassTabRow.kt` |
| Round glass action or toolbar control | `GlassIconButton` | `docs/components/glass/glassiconbutton.md` | `miuix-glass/.../glass/GlassIconButton.kt` |
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

This is a source-verified call shape, not a requirement to replace a higher-level Glass component with a custom modifier.

## BoxScope popup boundary

The `BoxScope` popup family does not register with `MiuixPopupHost`. It renders in the caller's root `BoxScope`, so the anchor, popup, and recorded page scene must share the coordinate space. Use `rememberGlassPopupAnchor()` and apply `Modifier.glassPopupAnchor(anchor, cornerRadius = ...)` to the outer control. Apply `glassPopupAnchorContent(anchor)` to the copied icon content when using `GlassTransformPopup` or `GlassIconDropdownMenu`; keep the popup composed during its exit animation.

`GlassIconDropdownMenu` uses the common `DropdownEntry`/`DropdownItem` model from `miuix-ui`. Its required parameters are `show`, `onDismissRequest`, `anchor`, `anchorContent`, `entries`, and `backdrop`; selection and item callbacks live in the `DropdownItem` values. `secondaryBackdrop` is for the cascading child scene and defaults to the supplied `backdrop`.

## Verified shell call shapes

The following calls are source-verified shapes for the current snapshot. They still require the target dependency, imports, and compilation check.

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

`GlassTopAppBar` uses the same `MiuixScrollBehavior` contract as the ordinary `TopAppBar`: the scrolling owner must attach its `nestedScrollConnection`. `progressiveBlur = true` is an explicit effect choice and requires a supported non-null backdrop; do not turn it on merely because the component is Glass.

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

### Glass icon dropdown menu

This is the current-main cascading menu shape. Unlike `GlassDropdownPreference`, it is not a preference row and does not require a `Scaffold` popup host:

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

## Do not infer

- `GlassNavigationBar` is not a replacement for route ownership or a general floating action.
- A `Backdrop` is not a color token and cannot be replaced with `Color.Transparent`.
- `miuix-glass` is not the same as `miuix-blur`: the former composes surfaces and controls; the latter provides the backdrop/effect primitives.
- `Glass` names are not aliases for ordinary Miuix names. Verify the module and package `top.yukonga.miuix.kmp.glass`.
- A glass component's `*Defaults` values are not page-level spacing rules. Use them only for the component contract.

When the task is about page ownership, shell placement, or the relationship between the recorded content and the bars, hand that part to `miuix-guidelines` and keep this reference for the Glass API/host contract.
