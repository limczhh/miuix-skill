# App Shell and Navigation Composition

Use when building a multi-destination app shell (`NavDisplay` + `HorizontalPager` + `NavigationBar` / `FloatingNavigationBar` / `NavigationRail`) or wiring `TabRow` / `GlassTabRow` / `GlassScrollableTabRow` / `GlassSegmentedTabRow` with a pager. Enforce [Greenfield invariants](rules/page-shell.md#greenfield-app-shell-invariants) and [Pager rules](rules/page-shell.md#tabs-and-pagers); verify exact Miuix imports and Defaults via `miuix-components`.

Contents: [default shell architecture](#default-shell-architecture) · [route vs selection](#route-vs-destination-selection) · [shell skeleton](#shell-scaffold-skeleton) · [selection coordinator](#selection-coordinator-and-tab-binding) · [expansion transitions](#route-container-expansion) · [first-pass delivery](#first-pass-implementation-and-acceptance)

## Default Shell Architecture

For 2–5 peer destinations + secondary pushed detail routes:

```text
AppTheme
└── AppRoot
    └── NavDisplay<Route>                         route stack owner (serializable NavBackStack)
        ├── Route.Shell
        │   └── AppShell                          persistent peer-destination owner
        │       └── Scaffold (Transparent container)
        │           ├── content Box (full-window surface / backdrop)
        │           │   └── HorizontalPager       receives innerPadding once; overscrollEffect = null
        │           │       └── DestinationPage   full-size sibling, owns its vertical scroll / page Scaffold
        │           └── bottomBar
        │               └── FloatingNavigationBar (or NavigationBar / wide-window Rail)
        └── Route.Detail(...)                      pushed secondary route + page Scaffold + onBack
```

*(Relation to upstream `Example`: `AppContent` adds an outer global-overlay `Scaffold` around `NavDisplay` + adaptive compact/wide chrome; omit outer wrappers until the product needs cross-bar global overlays or desktop rail morphing.)*

## Route vs Destination Selection

- **`Route` (stack identity)**: `@Serializable sealed interface Route : NavKey` (`Shell`, `Detail(id)`). Detail opens call `backStack.add(Route.Detail(id))` (preventing duplicate pushes). Pop handles local overlays first, then `Detail -> Shell`.
- **`Destination` (pager identity)**: `enum class Destination` (`Home`, `Library`, `Settings`). Bottom/rail taps mutate the shell selection coordinator, **never** `backStack.add(...)` — keeping one back-stack authority and surviving ordinary/floating/Glass/rail switches intact.
- Use `rememberNavBackStack<Route>(Route.Shell)` with `@Serializable` keys when process-death restoration is required.

## Shell Scaffold Skeleton

Verify actual imports (`top.yukonga.miuix.kmp.*`), `FloatingNavigationBar` color/modifier, and `HorizontalPager` gesture options via `miuix-components`.

```kotlin
@Composable
fun AppRoot() {
    val backStack = rememberNavBackStack<Route>(Route.Shell)
    NavDisplay(
        backStack = backStack,
        modifier = Modifier.fillMaxSize(),
        onBack = { if (backStack.size > 1) backStack.removeLastOrNull() },
    ) {
        entry<Route.Shell> {
            AppShell(onOpenDetail = { id -> backStack.add(Route.Detail(id)) })
        }
        entry<Route.Detail> { route ->
            DetailPage(id = route.id, onBack = { if (backStack.size > 1) backStack.removeLastOrNull() })
        }
    }
}

@Composable
fun AppShell(onOpenDetail: (String) -> Unit) {
    val destinations = remember { Destination.entries.toList() }
    val pagerState = rememberPagerState(pageCount = { destinations.size })
    val selection = rememberShellSelection(pagerState) // App-owned coordinator below

    Scaffold(
        modifier = Modifier.fillMaxSize(),
        containerColor = Color.Transparent,
        contentWindowInsets = WindowInsets(0),
        bottomBar = {
            Box(Modifier.fillMaxWidth()) {
                // Wrapper stays transparent/unclipped; FloatingNavigationBar owns shape & blur mask.
                FloatingNavigationBar(color = selectionBarColor, modifier = selectionBarModifier) { // App color + optional textureBlur
                    destinations.forEachIndexed { index, destination ->
                        FloatingNavigationBarItem(
                            selected = selection.selectedIndex == index,
                            onClick = { selection.select(index) },
                            icon = destination.icon,
                            label = destination.label,
                        )
                    }
                }
            }
        },
    ) { innerPadding ->
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(MiuixTheme.colorScheme.surface), // Record backdrop here when bar samples page
        ) {
            HorizontalPager(
                state = pagerState,
                modifier = Modifier
                    .fillMaxSize()
                    .padding(innerPadding)
                    .consumeWindowInsets(innerPadding),
                overscrollEffect = null, // Fixed horizontal page edge; child lists keep vertical overscroll
            ) { index ->
                DestinationPage(destination = destinations[index], onOpenDetail = onOpenDetail)
            }
        }
    }
}
```

## Selection Coordinator and Tab Binding

One application coordinator drives both peer bottom/rail nav and header `TabRow` / `GlassTabRow` / `GlassScrollableTabRow` / `GlassSegmentedTabRow` + `HorizontalPager`:

```text
Coordinator state: selectedIndex (initialized from currentPage), isNavigating = false, generation = 0, activeJob

select(target):
    validate target against current pages
    token = ++generation; cancel activeJob
    selectedIndex = target              // Synchronous update before coroutine launch
    isNavigating = true
    activeJob = createLazyJob {
        try {
            if (2 pages profile) smoothEaseInOutScrollTo(target) // ~300 ms EaseInOut; account pageSpacing
            else pagerState.scrollToPage(target)
        } finally {
            if (token == generation) {  // Stale cancelled job cannot clear newer lock
                isNavigating = false
                selectedIndex = pagerState.currentPage
                activeJob = null
            }
        }
    }; start activeJob

on pagerState.currentPage changed:
    if (!isNavigating) selectedIndex = pagerState.currentPage   // Never feed predictive targetPage

on real user swipe/drag/wheel/CrossAxis takeover:
    ++generation; cancel activeJob; activeJob = null
    isNavigating = false; selectedIndex = pagerState.currentPage
```

- **Why this matters**: `GlassSegmentedTabRow` settles its draggable indicator toward `selectedIndex` on pointer release, while Foundation `currentPage` lags early in a click transition and `targetPage` oscillates during drags/flings. The intent lock eliminates backward indicator snap on clicks (`0 -> 1 -> 0`) and swipe oscillation.
- Bind tabs using the shared coordinator (`modifier = Modifier.padding(horizontal = 12.dp).padding(bottom = 12.dp)` for standard strips): pass `selectedTabIndex`/`onTabSelected` on `TabRow`/`TabRowWithContour`, and `selectedIndex`/`onSelect` on `GlassTabRow`/`GlassScrollableTabRow`/`GlassSegmentedTabRow` (also verify CrossAxis pager flags via `miuix-components`).
- On tab removal/reordering, reconcile by stable page identity, cancel active requests, and update index before rendering.

## Route Container Expansion

When a card or item on one route should expand smoothly into a pushed detail destination, use `miuix-nav`'s `NavTransitions.Expansion` + `Modifier.navTransitionSource(...)` instead of ad-hoc shared-element hacks (verify exact signature, `containerColor`, corner matching, `sourceKey`, and `enableCornerClip` rules via `miuix-components`).

## First-Pass Implementation and Acceptance

Build in order: (1) typed `Route` + `NavDisplay` -> (2) transparent `AppShell` `Scaffold` + full-window bg + sibling `HorizontalPager` placeholders -> (3) `FloatingNavigationBar` in `bottomBar` -> (4) `SelectionCoordinator` + `overscrollEffect = null` -> (5) page content/top bars consuming shell padding once -> (6) detail routes & adaptive wide rail -> (7) blur/Glass polish.

First pass must deliver **real routes, real shell host, compilable placeholder pages, and defined coordinator helpers**, plus report the chosen ownership map. Accept only after verifying: immediate selected indicator without bounce, swipe takeover sync, continuous background behind floating bar (no full-width rectangle), single back pop without duplicate pushes, resize identity continuity, and RTL/large-text/short-window/IME/blur-off rendering.
