# Tab, Pager and Breadcrumb Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta01 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

## Ordinary tab rows

**Module:** `miuix-ui`

**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TabRow.kt`

**Docs/Demo:** `docs/components/basic/tabrow.md`, `docs/demo/src/commonMain/kotlin/TabRowDemo.kt`

Read the pinned documentation/source: [TabRow / TabRowWithContour](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/basic/tabrow.md).

Both expose controlled selection: callbacks report an index; the caller supplies `selectedTabIndex`. Supply non-empty tabs with a valid index when composing this page pattern; render an empty-content branch instead of calling pager/selection APIs with no valid page. `listState` controls only the horizontally scrolling strip, not the pager or any page's vertical list. `itemSpacing` is the internal tab gap; application header gutters and bottom spacing are separate.

For an ordinary row inside already blurred chrome, `TabRowDefaults.tabRowColors(backgroundColor = Color.Transparent)` removes its unselected fill while retaining the default selected fill and text roles. This factory also accepts `contentColor`, `selectedBackgroundColor`, and `selectedContentColor`; these are separate roles, not one blanket alpha applied to the whole strip.

In beta01, ordinary `TabRow` positions its selected background directly from `selectedTabIndex`; its selected-item reveal in the horizontal list can animate. `TabRowWithContour` animates its indicator offset from that external index with a 200 ms linear tween. Neither commits an optimistic selected index inside its click callback. `GlassTabRow` uses separately selectable pills; `GlassSegmentedTabRow` instead has a draggable shared indicator and release-time settling. Do not assume a common internal spring/click-selection implementation across these variants.

These APIs do not require a pager or impose a page-count animation policy. For pager-backed pages, use the application-owned [selection coordination pattern](../../../miuix-guidelines/references/navigation-compositions.md#tab-selection-and-page-switching) and its scoped design rules. Glass-specific notes are in [Glass tabs](glass.md#glass-tabs-tabrow--segmentedtabrow).

## Pager

**Module:** `miuix-ui`

**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/PagerGestureUtils.kt`

**Guide:** `docs/guide/utils.md`

The enum has only `PagerInterceptionMode.Native` and `PagerInterceptionMode.CrossAxis`. Read the modifier overloads and imports in the utility guide:

Read the pinned documentation/source: [Modifier.pagerGestureOverride](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/guide/utils.md).

The other overload takes the same required `pagerState` and `flingBehavior`, with required `mode: Int`; `enabled` and `onTriggered` match the enum overload. Invalid ordinals resolve to Native. Persist semantic mode identities when possible; normalize unsupported saved values to the product's chosen current mode and update pager configuration together. This overload does not accept a fling tracker.

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
    overscrollEffect = null,
    flingBehavior = flingBehavior,
    pageNestedScrollConnection = PagerGestureNestedScrollConnection,
) { page ->
    pageContent(page)
}
```

`PagerDefaults` / `HorizontalPager` are Compose Foundation APIs; verify them against the target's Foundation version too. Miuix's modifier replaces native touch recognition in this configuration and restores wheel, Shift+wheel and trackpad input using the supplied fling behavior. Rebound takeover recognizes Miuix overscroll modifiers/factory, not arbitrary third-party spring implementations.

For Native mode, omit the override and use normal `HorizontalPager` touch/nested-scroll defaults. A disabled override is a no-op: leaving `userScrollEnabled = false` would disable touch paging rather than restore native handling. Keep this invariant when mode/enabled changes at runtime. This is separate from a deliberately swipe-disabled onboarding flow.

Programmatic navigation may use `pagerState.springAnimateToPage(index)` from the same utility when spring motion is wanted. The design profile instead chooses a shared application EaseInOut helper for smooth navigation; see the [selection/motion pattern](../../../miuix-guidelines/references/navigation-compositions.md#tab-selection-and-page-switching). `animateToPage` on an application coordinator is not a Miuix API. Keep click/swipe cancellation in one owner. Do not import internal `pagerNonTouchScroll` or `OverscrollMotionNode` as application helpers.

Ordinary `TabRow` / `TabRowWithContour` consume horizontal nested scroll and fling only while their row can move in that direction, respect RTL/reverse layout, and disable row scrolling with no scroll range. Check this row/pager handoff before adding a custom consume-all interceptor.

`GlassSegmentedTabRow` increments its internal `settleGeneration` on pointer release and settles its indicator toward the externally supplied selection. A callback that only launches pager movement can leave that selection at the old page during release. The application coordinator holds the requested target during navigation, then follows `currentPage` when unlocked. This separates late click feedback from swipe feedback: `currentPage` is the nearest snap page and can still be old during a requested animation, while `targetPage` is a prediction that can change during gestures. Neither property alone defines a universal selection contract for every tab/navigation component.

### Pager edge effect ownership

`HorizontalPager` and its `overscrollEffect` parameter belong to Compose Foundation, not Miuix. The [Foundation declaration](https://developer.android.com/reference/kotlin/androidx/compose/foundation/pager/HorizontalPager.composable) defaults this parameter to `rememberOverscrollEffect()`; verify the resolved Foundation version in the consumer project.

In pinned beta01, `MiuixTheme.kt` supplies `MiuixOverscrollFactory` through `LocalOverscrollFactory`. `utils/OverscrollFactory.kt` creates the effect and applies its X/Y offsets as layer translation during placement. Under that theme, a pager using the default effect can move its content at the first/last edge. The example above chooses `overscrollEffect = null` for fixed page edges, matching the [peer-page design profile](../../../miuix-guidelines/references/rules/page-shell.md#tabs-and-pagers).

The null argument disables only that pager effect. Explicit `overScrollHorizontal`, an ancestor overscroll node or a custom transform remains a separate owner. Child vertical-list overscroll, refresh and app-bar motion are separate too; do not null the entire theme's factory to implement this local recipe. Selection coordination and edge-effect gating solve different feedback paths.

## Breadcrumb

**Module:** `miuix-ui`

**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/BreadcrumbBar.kt`

**Docs/Demo:** `docs/components/basic/breadcrumbbar.md`, `docs/demo/src/commonMain/kotlin/BreadcrumbBarDemo.kt`

Read the pinned documentation/source: [BreadcrumbBar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/basic/breadcrumbbar.md).

Use `top.yukonga.miuix.kmp.basic.BreadcrumbBar` / `BreadcrumbItem`, with Compose Foundation `rememberLazyListState` when owning the row position. `listState` is optional and dedicated to the horizontal breadcrumb; it is not the page's vertical list state. The callback index selects a breadcrumb, not a navigation route by itself. Existing `ScrollState` pixel offsets are not drop-in `LazyListState` positions.

## Verification

Compile the changed target. Separately exercise horizontal/vertical drags, child fling/rebound takeover, tab-click cancellation, native-mode fallback, wheel/trackpad, RTL and breadcrumb reveal/restoration. A matching declaration does not prove input behavior on each platform.
