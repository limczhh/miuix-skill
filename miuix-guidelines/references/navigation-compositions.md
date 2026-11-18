# Navigation, Tabs and Action Composition Guide

Use this guide to choose the placement of persistent navigation or host-owned actions. Mandatory state, material, spacing, and adaptive behavior live in [Navigation rules](rules/page-shell.md#navigation-and-adaptive-layout); [page-host rules](rules/page-shell.md#page-hosts-and-shared-wrappers) own slot responsibilities.

## Destination or action?

| User intent | Composition shape | State/host to identify |
|---|---|---|
| Switch top-level destinations | `NavigationBar` in the shell bottom slot | Shell destination/pager owner |
| Same navigation, visually detached from edge | `FloatingNavigationBar` in the same slot | Same owner and shell backdrop policy |
| Side navigation for the chosen layout mode | `NavigationRail` in the shell side layout | Same selection, adaptive policy, content space handoff |
| Switch peer content within one page | Fixed/header `TabRow` family + `HorizontalPager` | One tab/pager selection coordinator |
| One page action | Host `floatingActionButton` slot | Page action callback |
| Related page action group | Host `floatingToolbar` slot | Page action group and position policy |
| Transient operation message | Host `snackbarHost` path | Active page/window feedback owner |

The [standard bar material policy](rules/page-shell.md#blur-and-glass) applies to normal bottom/floating navigation. Existing component Defaults determine the actual bar shape and inset behavior after target verification.

## Shell placement

```text
shell: selected destination and navigation callbacks
└── layout policy
    ├── compact: Scaffold.bottomBar → blur wrapper/floating mask → navigation
    └── side: NavigationRail + available content region
        └── active page, using the shell padding handoff
```

A style or layout switch changes chrome placement. Use the [adaptive rules](rules/page-shell.md#navigation-and-adaptive-layout) to assess state continuity and the [inset rules](rules/page-shell.md#insets-and-content-padding) to assess the page handoff. Their exact dimensions are defined there, not repeated in this guide.

The bar and navigation share the same actual-window decision; landscape alone does not select a rail. For floating navigation, inspect built-in item spacing before adding content children: a Spacer becomes another arranged child and can duplicate the bar's own gap. A sibling rail's reserved start side is part of the content handoff, not a second page gutter.

## Tab selection and page switching

The [pager rules](rules/page-shell.md#tabs-and-pagers) define placement, spacing, motion and edge treatment for peer pages. One application-owned coordinator instance serves TabRow, bottom/floating navigation and a resized rail when they control the same `HorizontalPager`. Reuse the same coordinator type across pages, with one instance per pager; a `NavPagerState`-style helper is a useful name, not a Miuix API. A control without a pager can simply update its controlled selection or existing route owner.

Distinguish the selected navigation intent from the pager's intermediate position:

| State | Meaning and use |
|---|---|
| Application `selectedIndex` | Single visual selection consumed by every navigation presentation |
| Application `isNavigating` | Intent lock: holds the selected click/programmatic target while its pager request is active |
| `pagerState.currentPage` | Page nearest the snap position; can still be the old page early in an animation and changes during movement |
| `pagerState.targetPage` | Proposed stopping page; can change as drag/fling predictions change, so it does not drive this profile's indicator |
| `pagerState.settledPage` | Last settled page; useful for settled-only effects, not immediate click feedback |

These Foundation meanings are documented in [Pager state changes](https://developer.android.com/develop/ui/compose/layouts/pager#get-notified-about-page-state-changes). Initialize `selectedIndex` from `currentPage`. A navigation click updates `selectedIndex` synchronously and locks pager-to-selection feedback. When unlocked, observe `currentPage` and synchronize the index; this follows the actual nearest page rather than fluctuating predicted destinations. `currentPage` alone is insufficient during a navigation animation, while `targetPage` alone also feeds prediction changes back into the indicator. The intent lock separates these two paths. Settled business actions and destructive commands resolve the intended page/data identity explicitly rather than treating visual selection as proof of settlement.

### Coordinator event flow

The following is application pseudocode, not a Miuix API or a required class name. State changes run on the UI owner; `createLazyJob` means the job is stored before it can execute. Both switch modes share the same lifecycle:

```text
select(page):
    validate page against current tab identities/count
    mode = chosen page-count/flow policy
    token = ++generation
    cancel activeJob
    selectedIndex = page             // synchronous, before coroutine launch
    isNavigating = true
    activeJob = createLazyJob {
        try:
            if mode is smooth:
                smoothToPage(page)   // shared application EaseInOut helper
            else:
                pagerState.scrollToPage(page)
        finally:
            if token == generation:  // stale job cannot unlock a new request
                isNavigating = false
                selectedIndex = pagerState.currentPage
                activeJob = null
    }
    start activeJob

on pagerState.currentPage changed:
    if not isNavigating:
        selectedIndex = pagerState.currentPage

on actual user pager takeover:
    ++generation
    cancel activeJob
    activeJob = null
    isNavigating = false
    selectedIndex = pagerState.currentPage
```

Choose the two-page/multiple-page branch from the [motion policy](rules/page-shell.md#tabs-and-pagers), with the same helper for top tabs and bottom navigation. The shared smooth helper uses Compose animation's `tween(easing = EaseInOut, durationMillis = ...)`; Foundation's default `animateScrollToPage(page)` animation or Miuix's `springAnimateToPage` does not by itself implement this chosen curve. Direct switching still runs in a coroutine and needs the request guard; clearing `isNavigating` before `scrollToPage` completes reopens the stale-feedback window.

For a custom smooth helper, compute displacement using page size **plus page spacing** and the current fractional offset, then consume incremental motion through the pager's scroll scope. Preserve cancellation and input arbitration rather than catching cancellation and forcing a final jump. If a successful animation needs a final snap correction, perform it only while that request still owns the job. Prefer an existing helper with these semantics over duplicating animation code in each page. `EaseInOut`, `tween`, `PagerState.scroll` and `scrollToPage` are Compose APIs; `NavPagerState`, `animateToPage` and `smoothToPage` here are application abstractions.

Use actual input takeover to interrupt a request. `isScrollInProgress` alone cannot identify a user gesture because programmatic animation also sets it. For native paging, use the pager's actual drag interaction; for CrossAxis touch takeover, the verified modifier exposes `onTriggered`. Check the chosen wheel/trackpad path separately. Do not attach an always-on raw pointer interceptor merely to cancel jobs. External routes and accessibility/keyboard selections enter the same request path. A repeated selection can be ignored only when the desired target is already satisfied or owned by the active request.

On tab removal/reordering, reconcile by stable page identity, cancel invalid requests and resolve the resulting pager index before rendering. Do not only clamp a pending integer to a different logical tab. Keep each page's list/data state outside ordinary/Glass presentation branches. Dispose with the page owner; restore logical page identity/pager position, not an in-flight job or selection lock.

### Bind one selection to either family

Assume `tabs`, `pagerState` and the application-owned `selection` implement the event flow above. `selection.selectedIndex` exposes the resolved visual index; `selection.select` chooses the profile/flow motion policy. The header can use either call shape. Bottom/floating/rail items use `selected = selection.selectedIndex == index` and route clicks to the same `selection.select(index)`; Glass navigation uses that index through `selectedIndex` / `onSelect`.

```kotlin
TabRow(
    tabs = tabs,
    selectedTabIndex = selection.selectedIndex,
    onTabSelected = selection::select,
    modifier = Modifier.padding(horizontal = 12.dp).padding(bottom = 12.dp),
)
// Alternative presentation, with the same owner and a verified non-null backdrop:
GlassSegmentedTabRow(
    tabs = tabs,
    selectedIndex = selection.selectedIndex,
    onSelect = selection::select,
    backdrop = backdrop,
    modifier = Modifier.padding(horizontal = 12.dp).padding(bottom = 12.dp),
)
// Primary content, separate from the header; keep the existing inset/gesture contract:
HorizontalPager(
    state = pagerState,
    overscrollEffect = null,
) { page -> pageContent(page) }
```

The snippet shows the native pager call shape. CrossAxis projects keep their matching [gesture configuration](../../miuix-components/references/contracts/pager-and-breadcrumb.md#pager). Ordinary `TabRowWithContour` uses the ordinary selection/callback names; `GlassTabRow` uses the Glass names. Material/background policy remains separate from state coordination. Exact ordinary declarations and variant behavior are in [Ordinary tab rows](../../miuix-components/references/contracts/pager-and-breadcrumb.md#ordinary-tab-rows); the segmented row has an independently moving indicator, so late feedback can visibly pull it toward the old page on release. Do not attribute that implementation to every TabRow variant.

### Pager edge treatment

Use the local `overscrollEffect = null` argument shown above to preserve this profile's fixed horizontal page edges. Trace explicit horizontal overscroll modifiers and parent transforms if background is still exposed. Child vertical lists and top-bar pull/rebound remain under their own owners; preserve their behavior rather than disabling the theme's overscroll factory globally. The theme-provided effect and Compose parameter are explained in [effect ownership](../../miuix-components/references/contracts/pager-and-breadcrumb.md#pager-edge-effect-ownership).

For interaction validation, exercise a click before the pager crosses its snap boundary, rapid `0 → 1 → 0` requests, a swipe interrupting a click, slow back-and-forth drags/fling cancellation, a non-adjacent direct switch, and ordinary/Glass or bar/rail presentation changes. Verify that selection stays locked to the active navigation request, unlocked selection follows `currentPage` without predictive-target oscillation, and each page retains its list state. Drag beyond the first and last pages and confirm the pager edge stays fixed while child vertical overscroll/refresh still works. These require rendered/device evidence in the consumer project; source inspection alone does not prove animation smoothness or the absence of exposed background.

## API handoff and delivery

After choosing the shape, verify only the selected bar/item scopes, floating slot parameters, corner Defaults, and inset flags through `miuix-components`. Public parameter spelling and default values belong in that API contract, not this guide.

For pager-backed navigation, use [Tabs and pagers](rules/page-shell.md#tabs-and-pagers). For competing gestures, use [Gesture dynamics](gesture-dynamics.md). Report route/selection, resized layout, inset, blur-fallback, and rendering evidence using the [page-shell validation boundary](rules/page-shell.md#validation).
