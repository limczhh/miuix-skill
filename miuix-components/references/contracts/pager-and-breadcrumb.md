# Pager and Breadcrumb Contracts

Catalog-verified for `v0.9.5-beta01`, commit `6d2554a850b8960e2616c968263ff64beb5ffb03`. Use for exact gesture configuration or breadcrumb list state. Target resolution and compilation remain separate checks.

## Pager

**Module:** `miuix-ui`

**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/PagerGestureUtils.kt`

**Guide:** `docs/guide/utils.md`

The enum has only `PagerInterceptionMode.Native` and `PagerInterceptionMode.CrossAxis`. The primary overload is:

```kotlin
fun Modifier.pagerGestureOverride(
    pagerState: PagerState,
    flingBehavior: FlingBehavior,
    mode: PagerInterceptionMode = PagerInterceptionMode.CrossAxis,
    enabled: Boolean = true,
    onTriggered: (() -> Unit)? = null,
): Modifier
```

The other overload takes the same required `pagerState` and `flingBehavior`, with required `mode: Int`; `enabled` and `onTriggered` keep these defaults. Invalid ordinals resolve to Native. Persist semantic mode identities when possible; normalize unsupported saved values to the product's chosen current mode and update pager configuration together. This overload does not accept a fling tracker.

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.utils.PagerGestureNestedScrollConnection
import top.yukonga.miuix.kmp.utils.PagerInterceptionMode
import top.yukonga.miuix.kmp.utils.PagerNavigationSpringSpec
import top.yukonga.miuix.kmp.utils.pagerGestureOverride
import top.yukonga.miuix.kmp.utils.springAnimateToPage
```

CrossAxis call shape, with the caller's remembered `pagerState` and existing page content:

```kotlin
val flingBehavior = PagerDefaults.flingBehavior(
    state = pagerState,
    snapAnimationSpec = PagerNavigationSpringSpec,
)
HorizontalPager(
    state = pagerState,
    modifier = Modifier.pagerGestureOverride(
        pagerState = pagerState,
        flingBehavior = flingBehavior,
        mode = PagerInterceptionMode.CrossAxis,
    ),
    userScrollEnabled = false,
    flingBehavior = flingBehavior,
    pageNestedScrollConnection = PagerGestureNestedScrollConnection,
) { page ->
    pageContent(page)
}
```

`PagerDefaults` / `HorizontalPager` are Compose Foundation APIs; verify them against the target's Foundation version too. Miuix's modifier replaces native touch recognition in this configuration and restores wheel, Shift+wheel and trackpad input using the supplied fling behavior. Rebound takeover recognizes Miuix overscroll modifiers/factory, not arbitrary third-party spring implementations.

For Native mode, omit the override and use normal `HorizontalPager` touch/nested-scroll defaults. A disabled override is a no-op: leaving `userScrollEnabled = false` would disable touch paging rather than restore native handling. Keep this invariant when mode/enabled changes at runtime. This is separate from a deliberately swipe-disabled onboarding flow.

Tab-click navigation may use `pagerState.springAnimateToPage(index)` from the same utility. Keep click/swipe cancellation in one owner. Do not import internal `pagerNonTouchScroll` or `OverscrollMotionNode` as application helpers.

Ordinary `TabRow` / `TabRowWithContour` consume horizontal nested scroll and fling only while their row can move in that direction, respect RTL/reverse layout, and disable row scrolling with no scroll range. Check this row/pager handoff before adding a custom consume-all interceptor.

When pairing `HorizontalPager` with `GlassSegmentedTabRow`:
- Always bind `GlassSegmentedTabRow(selectedIndex = pagerState.targetPage, ...)` rather than `pagerState.currentPage`.
- `GlassSegmentedTabRow` supports horizontal drag gestures and resets its indicator position via an internal `settleGeneration` counter immediately upon pointer release. Because `pagerState.currentPage` updates only after the pager finishes scrolling or crosses the halfway threshold, passing `currentPage` forces the indicator to snap backward toward the un-settled page on release before springing forward again.

## Breadcrumb

**Module:** `miuix-ui`

**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/BreadcrumbBar.kt`

**Docs/Demo:** `docs/components/basic/breadcrumbbar.md`, `docs/demo/src/commonMain/kotlin/BreadcrumbBarDemo.kt`

```kotlin
@Composable
fun BreadcrumbBar(
    items: List<BreadcrumbItem>,
    onItemClick: (Int) -> Unit,
    modifier: Modifier = Modifier,
    highlightIndex: Int = items.lastIndex,
    enabled: Boolean = true,
    colors: BreadcrumbBarColors = BreadcrumbBarDefaults.breadcrumbBarColors(),
    insideMargin: PaddingValues = BreadcrumbBarDefaults.InsideMargin,
    itemMaxWidth: Dp = BreadcrumbBarDefaults.ItemMaxWidth,
    listState: LazyListState? = null,
    interactionSource: MutableInteractionSource? = null,
    indication: Indication? = LocalIndication.current,
)
```

Use `top.yukonga.miuix.kmp.basic.BreadcrumbBar` / `BreadcrumbItem`, with Compose Foundation `rememberLazyListState` when owning the row position. `listState` is optional and dedicated to the horizontal breadcrumb; it is not the page's vertical list state. The callback index selects a breadcrumb, not a navigation route by itself. Existing `ScrollState` pixel offsets are not drop-in `LazyListState` positions.

## Verification

Compile the changed target. Separately exercise horizontal/vertical drags, child fling/rebound takeover, tab-click cancellation, native-mode fallback, wheel/trackpad, RTL and breadcrumb reveal/restoration. A matching declaration does not prove input behavior on each platform.
