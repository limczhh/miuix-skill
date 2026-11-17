# Top Bar Compositions

Use when a page needs a pinned title or a large title that responds to the primary content scroll.

Do not use this guide as a full page template. If the page already has a shared shell or scroll helper, first read [Brownfield evolution](brownfield-evolution.md). If pull-to-refresh, explicit overscroll, a pager, or rebound-driven visuals participate, use [Gesture dynamics](gesture-dynamics.md) before changing modifiers.

The page host owns the app-bar slot. The primary content owns the vertical movement. A shared MiuixScrollBehavior is the bridge only for the basic collapsing-bar case; it is not proof that a multi-part gesture chain is valid.

Before implementation, send only the concrete Miuix symbols used by the chosen shape to miuix-components for source verification. After implementation, compile the affected target when available and separately report inset, scroll, accessibility, and device/render evidence.

## Pinned top bar and one scroll owner

Use this shape when the title should remain visible while the page scrolls. `SmallTopAppBar` is the pinned app-bar form.

```kotlin
Scaffold(
    topBar = {
        SmallTopAppBar(
            title = "Page",
        )
    },
) { innerPadding ->
    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .consumeWindowInsets(innerPadding),
        contentPadding = innerPadding,
    ) {
        // Page content owns the vertical scroll.
    }
}
```

Use a `Column` with `verticalScroll` instead when the content is a small, naturally linear form. Do not put a `LazyColumn` inside that `Column` just to make one subsection scroll.

If the page has no bar, no bottom/floating slot, and no host-owned transient layer, a plain content root may be more appropriate than `Scaffold`.

## Collapsing top bar

Use this shape when a large title should collapse into the app-bar area as the primary content scrolls. The same `MiuixScrollBehavior` instance belongs to both the app bar and the scroll container.

```kotlin
val scrollBehavior = MiuixScrollBehavior()

Scaffold(
    topBar = {
        TopAppBar(
            title = "Page",
            largeTitle = "Large page title",
            scrollBehavior = scrollBehavior,
        )
    },
) { innerPadding ->
    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .nestedScroll(scrollBehavior.nestedScrollConnection)
            .consumeWindowInsets(innerPadding),
        contentPadding = innerPadding,
    ) {
        // Page content.
    }
}
```

The `nestedScrollConnection` is part of the behavior contract, not an optional visual tweak. Do not create one behavior for `TopAppBar` and another for `LazyColumn`. Keep `LazyListState` for list position and `TopAppBarState`/`ScrollBehavior` for app-bar motion; they are related through nested scroll, not interchangeable.

Use `SmallTopAppBar` instead when the title must remain pinned. Do not add collapsing behavior to a page whose hierarchy does not benefit from a large-title transition.

If the page also has pull-to-refresh, explicit overscroll, a pager, or a visual band/tab response to rebound, stop treating this as the basic collapsing-bar recipe. Read [Gesture dynamics](gesture-dynamics.md) and choose one nested-scroll topology whose target source contract is verified before adding modifiers. The basic snippet does not prove that multiple gesture participants can be attached independently.
