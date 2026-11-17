# Glass Compositions

Use only when the product explicitly asks for Miuix Glass surfaces, backdrop sampling, a Glass bar, or a Glass overlay.

Do not use this guide to turn an ordinary Miuix page translucent by default. For exact Glass modules, imports, overloads, backdrop APIs, and popup-host rules, read the matching contract in miuix-components after deciding the page boundary.

Glass adds a visual sampling boundary to the ordinary page skeleton. It does not replace theme, route, host, inset, state, or gesture ownership.

Before implementation, send the exact Glass symbols, backdrop APIs, and popup-host questions to miuix-components. After implementation, validate the recorded-layer boundary, fallback branch, insets, and rendered/device behavior separately; source inspection alone cannot prove blur or sampling quality.

## Optional Glass shell

Use this recipe only when the product explicitly asks for Miuix's optional Glass family. It adds a backdrop-recording boundary to the ordinary page skeleton; it does not replace the ownership rules above or make every Miuix page translucent.

```kotlin
val backdrop = rememberLayerBackdrop()
val listState = rememberLazyListState()
val scrollBehavior = MiuixScrollBehavior()

Scaffold(
    topBar = {
        GlassTopAppBar(
            title = "Page",
            isContentScrolled = listState.canScrollBackward,
            backdrop = backdrop,
            scrollBehavior = scrollBehavior,
        )
    },
    bottomBar = {
        GlassNavigationBar(
            items = destinations,
            selectedIndex = selectedIndex,
            onSelect = onDestinationSelected,
            backdrop = backdrop,
        )
    },
) { innerPadding ->
    LazyColumn(
        state = listState,
        modifier = Modifier
            .fillMaxSize()
            .nestedScroll(scrollBehavior.nestedScrollConnection)
            .layerBackdrop(backdrop)
            .background(MiuixTheme.colorScheme.surface),
        contentPadding = innerPadding,
    ) {
        // Page content is the recorded background.
    }
}
```

The exact Glass imports, overloads, and dependency boundary belong to the companion [Glass Components](../../miuix-components/references/glass.md) reference. The composition rules are:

- record an opaque page/content layer with `rememberLayerBackdrop` + `Modifier.layerBackdrop`;
- keep `GlassTopAppBar`, `GlassNavigationBar`, and their action controls outside that recorded layer, then pass the same `backdrop` to them;
- keep the same `MiuixScrollBehavior` attached to the Glass top bar and the primary scroll owner;
- keep `selectedIndex` and destination callbacks in the shell/route owner, not in `GlassNavigationBar`;
- use `null` backdrop only as an intentional opaque fallback, not as a substitute for setting up sampling;
- do not enable `progressiveBlur` or custom material/strokes until the basic fallback and host/inset behavior are correct.

When the user only wants a normal Miuix page, use the ordinary top-bar/navigation recipes instead. When the user wants a Glass dialog, sheet, or `GlassDropdownPreference`, keep it under the intended `Scaffold` and apply the backdrop exclusion rules from the components reference. `GlassPopup`/`GlassTransformPopup`/`GlassIconDropdownMenu` are different `BoxScope` components; keep them beside their anchor in the same root box.

### Glass backdrop with a pinned small title

When the product needs the Glass backdrop/band behavior but the visible title must use the pinned SmallTopAppBar layout, a composite host can keep the two responsibilities separate:

    Box {
        GlassTopAppBar(
            modifier = Modifier.matchParentSize(),
            title = "",
            largeTitle = "",
            isContentScrolled = contentScrolled,
            backdrop = backdrop,
            scrollBehavior = null,
            bandBrush = bandBrush,
            backgroundAlpha = { if (showBand) 1f else 0f },
        )
        SmallTopAppBar(
            title = title,
            scrollBehavior = scrollBehavior,
            navigationIcon = navigationIcon,
            actions = actions,
        )
    }

The lower GlassTopAppBar is a transparent/empty visual layer that owns backdrop sampling and the band; the SmallTopAppBar owns the visible title and actions. Here showBand is application-owned visual state, not a Miuix parameter. This is an application composition workaround, not a new Miuix component or a reason to attach the same scroll connection twice. Keep both in the same Scaffold topBar, use one page backdrop, and verify the exact Glass overload and insets against miuix-components. If the normal GlassTopAppBar layout already satisfies the requirement, use it instead.
