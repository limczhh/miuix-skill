# Bars, Scroll, Refresh, Pagers, and Navigation Chrome

Read pinned official docs at `0.9.5-rc01` for full parameter tables; this reference retains nested-scroll topologies, inset flags, pager edge ownership, and parameter spelling traps.

Contents: [TopAppBar](#top-app-bar-and-scrollbehavior) · [PullToRefresh & overscroll](#pulltorefresh-and-overscroll) · [ScrollBars](#scrollbars) · [TabRow & Pager](#tabrow-and-pager) · [BreadcrumbBar](#breadcrumbbar) · [Cards & SmallTitle](#cards-and-smalltitle) · [NavigationBar & Floating](#navigationbar-and-floatingnavigationbar)

## Top App Bar and ScrollBehavior

Doc: `docs/components/basic/topappbar.md`, demo: `docs/demo/TopAppBarDemo.kt`, source: `miuix-ui/.../basic/TopAppBar.kt`.

- `TopAppBar` / `SmallTopAppBar` take `title: String`, nullable `scrollBehavior: ScrollBehavior?`, and `RowScope` actions. `TopAppBar(expandable = false)` keeps a compact title visible at rest; `SmallTopAppBar` is the pinned compact form (no `largeTitle` parameter). Both expose `centerObstructionStart` for reserving space against a centered overlay such as a collapsed rail (`TopAppBarDefaults.TitleControlSpacing = 12.dp`).
- **Low-level `TopAppBarLayout`**: Public in `top.yukonga.miuix.kmp.basic` when custom `titleBlurRadius` is needed without adopting `GlassTopAppBar` (inspect exact required params in `TopAppBar.kt`). Read `titleAlpha` in its deferred callback.
- **Top insets & background**: Both ordinary layouts apply `.background(color)` **before** `.windowInsetsPadding(WindowInsets.systemBars.only(WindowInsetsSides.Top))`. `defaultWindowInsetsPadding = false` only disables **horizontal** display-cutout/navigation-bar padding; the internal top system-bar inset stays active unless already consumed by an ancestor.
- **Collapsing behavior (`MiuixScrollBehavior`)**: The scrollable child must attach `scrollBehavior.nestedScrollConnection` (note `rc01` `topappbar.md` connects `.nestedScroll(...)` directly without redundant `.overScrollVertical()`). `TopAppBarState` exposes `overscrollOffset: Float`, `visualContentOffset: Float`, and snapshot-backed `heightOffsetLimit`. Note `GlassTopAppBar` takes `modifier` (outer frame) + `contentModifier` (inner title/controls) and does **not** accept `visualContentOffset`; its `BandOverhang` is `32.dp`.

## PullToRefresh and Overscroll

Doc: `docs/components/basic/pulltorefresh.md`, utility guide: `docs/guide/utils.md`.

Choose **one** top-bar integration topology — never attach `scrollBehavior.nestedScrollConnection` in both `PullToRefresh` and the inner list:

```kotlin
// Topology A: PullToRefresh coordinates topAppBarScrollBehavior directly
PullToRefresh(
    isRefreshing = isRefreshing,
    onRefresh = onRefresh,
    contentPadding = innerPadding,
    topAppBarScrollBehavior = scrollBehavior,
) {
    LazyColumn(contentPadding = innerPadding) { /* Do NOT attach scrollBehavior.nestedScrollConnection here */ }
}

// Topology B: List/helper owns both overScrollVertical bridge and nestedScrollConnection
PullToRefresh(
    isRefreshing = isRefreshing,
    onRefresh = onRefresh,
    contentPadding = innerPadding, // Leave topAppBarScrollBehavior null
) {
    LazyColumn(
        modifier = Modifier
            .overScrollVertical(onOffsetChanged = { scrollBehavior.state.overscrollOffset = it })
            .nestedScroll(scrollBehavior.nestedScrollConnection),
        contentPadding = innerPadding,
    ) { /* Content */ }
}
```

- `PullToRefreshState` owns drag, indicator, threshold (`rememberPullToRefreshState(refreshThreshold = 0.25f)` default), and completion motion. `onPullProgress` reports full-range `fullDragProgress` (`0f..1f`), including completion animations. Hoist `isRefreshing` logically; do not derive it from overscroll pixels.
- `Modifier.overScrollVertical`, `overScrollHorizontal`, and `overScrollOutOfBound` take `onOffsetChanged` (including spring rebound and zero reset, without a `nestedScrollToParent` flag). Note theme-provided `LocalOverscrollFactory` (`MiuixOverscrollFactory`) is separate from explicit overscroll modifiers.

## ScrollBars

Source: `miuix-ui/.../basic/ScrollBar.kt` (no dedicated doc; see `example/shared/.../SettingsPage.kt`, `MainPage.kt`).

- `VerticalScrollBar`, `HorizontalScrollBar`, `Modifier.verticalScrollBar`, and `Modifier.horizontalScrollBar` require `@OptIn(ExperimentalScrollBarApi::class)`.
- `rememberScrollBarAdapter(scrollState: ScrollableState, reverseLayout = false)` supports `ScrollState`, lazy list/grid/staggered-grid, `PagerState`, and custom `ScrollIndicatorState`. Prefer the modifier wrappers on containers when possible (`reverseLayout` on modifier is compatibility-only).
- Default touch target is `ScrollBarDefaults.TouchTargetWidth = 24.dp`; pass `trackPadding = contentPadding` so the bar stays outside padded/header/footer areas as a sibling overlay.

## TabRow and Pager

Doc: `docs/components/basic/tabrow.md` (`TabRow.kt`) and `docs/guide/utils.md` (`PagerGestureUtils.kt`).

- **Controlled tabs**: `TabRow` positions its selected background directly from `selectedTabIndex` (list reveal animates); `TabRowWithContour` animates its indicator with a 200 ms linear tween; both derive widths from live measured space. Neither commits an optimistic index in `onTabSelected`. Inside blurred bars, `TabRowDefaults.tabRowColors(backgroundColor = Color.Transparent)` clears the strip fill without altering selected/text roles.
- **`PagerGestureUtils`**: `PagerInterceptionMode` only has `Native` and `CrossAxis` (raw `Int` overload resolves invalid ordinals to `Native`). `CrossAxis` requires **all four** coordinated settings on `HorizontalPager` + modifier:

```kotlin
val flingBehavior = PagerDefaults.flingBehavior(state = pagerState, snapAnimationSpec = PagerNavigationSpringSpec)
HorizontalPager(
    state = pagerState,
    modifier = Modifier.pagerGestureOverride(pagerState = pagerState, flingBehavior = flingBehavior, mode = PagerInterceptionMode.CrossAxis),
    userScrollEnabled = false, // Must restore true when switching back to Native or disabling override
    overscrollEffect = null,   // Suppresses MiuixOverscrollFactory horizontal translation at first/last page edge
    flingBehavior = flingBehavior,
    pageNestedScrollConnection = PagerGestureNestedScrollConnection,
) { page -> /* Content */ }
```

- Programmatic spring snap is available via `pagerState.springAnimateToPage(index)`. Do not import internal `pagerNonTouchScroll` or `OverscrollMotionNode`.

## BreadcrumbBar

Doc: `docs/components/basic/breadcrumbbar.md` (`BreadcrumbBar.kt`): pass optional Compose Foundation `LazyListState` (separate from page vertical list state). Callback reports item index only.

## Cards and SmallTitle

Doc: `docs/components/basic/card.md`, `smalltitle.md`:
- `Card` has static and clickable/long-press overloads (use named args for the interactive overload). For cards inside Glass sheets/dialogs, use [`GlassCard`](glass-and-blur.md#glass-overlays-and-glasscard).
- Pinned defaults: `CardDefaults.CornerRadius = 16.dp`, `CardDefaults.InsideMargin = PaddingValues(0.dp)`, `SmallTitleDefaults.InsideMargin = PaddingValues(28.dp, 8.dp)`.

## NavigationBar and FloatingNavigationBar

Doc: `docs/components/basic/navigationbar.md` (`NavigationBar.kt`):
- Designed for **2–5 items**; `NavigationBarItem` / `FloatingNavigationBarItem` are `RowScope` extensions. Pass an `ImageVector` to `itemIcon`, and name `badge` (because `colors` appears before `badge`).
- Put `FloatingNavigationBar` in `Scaffold.bottomBar` (not an arbitrary overlay box). Watch parameter spelling **`horizontalOutSidePadding`** (capital `S`, applied for `Alignment.Start` / `Alignment.End`). When `defaultWindowInsetsPadding = true` (default), it handles both horizontal cutout/nav-bar padding and bottom nav/caption spacing.
