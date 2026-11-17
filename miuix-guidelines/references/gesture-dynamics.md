# Gesture Dynamics

Use this reference when one page combines two or more of scrolling, pull-to-refresh, overscroll, a collapsing or Glass top bar, a tab row, a pager, a sheet, or custom drag behavior. The ordinary rule “create a ScrollBehavior and attach its nested-scroll connection” is necessary for a basic collapsing bar, but it is not enough to reason about a multi-part gesture chain.

This reference describes an audit model, not one universal modifier order. The actual Miuix version and the target project's existing helper decide which topology is valid.

## Current source contracts

At the current source-audit snapshot:

- MiuixScrollBehavior exposes a nested-scroll connection. A scrolling content owner must participate in that connection for a top bar to collapse or expand.
- PullToRefresh is an active nested-scroll and pointer-gesture container. It hoists logical isRefreshing, owns PullToRefreshState for indicator/animation state, and invokes onRefresh when the gesture crosses its state machine.
- PullToRefresh also accepts an optional topAppBarScrollBehavior. Its current implementation coordinates the app-bar connection with the pull-to-refresh/overscroll connection and installs the resulting connection on its own modifier.
- overScrollVertical accepts an onOffsetChanged callback. The callback observes the damped pixel offset, including spring frames and reset-to-zero; it is not the consumed scroll position.
- TopAppBarState.overscrollOffset is a visual overscroll value in the current source. It contributes to visualContentOffset, but it is not the saved list position or the logical refresh state.
- The current Miuix utility guide places the top-bar nested-scroll modifier after overScrollVertical. It keeps contentOffset as actual list movement and overscrollOffset as damped displacement; visualContentOffset combines them. It also warns not to pass the same behavior to an enclosing PullToRefresh when the list already owns the connection.

These are source/version facts, not permission to attach every connection at every level. For exact signatures and a different Miuix version, hand the narrow symbols to miuix-components.

For a source audit, start with miuix-ui/.../basic/PullToRefresh.kt, miuix-ui/.../utils/Overscroll.kt, miuix-ui/.../basic/TopAppBar.kt, then compare the integrated topology in example/shared/src/commonMain/kotlin/PullToRefreshPage.kt and example/shared/src/commonMain/kotlin/utils/PageUtils.kt.

The current utility guide's concrete list shape is:

    val scrollState = scrollBehavior.state
    val onOverscrollChanged: (Float) -> Unit = remember(scrollState) {
        { scrollState.overscrollOffset = it }
    }

    LazyColumn(
        modifier = Modifier
            .overScrollVertical(onOffsetChanged = onOverscrollChanged)
            .nestedScroll(scrollBehavior.nestedScrollConnection),
    ) {
        // List content
    }

Keep the top-bar connection after overScrollVertical in this topology. Verify the target declaration and whether the scrollable's default overscroll effect must be disabled before copying the shape.

## First map the participants

Before editing, write the actual chain:

    pointer drag / wheel / keyboard
        -> primary scroll owner
        -> top-bar connection
        -> pull-to-refresh connection
        -> overscroll effect and spring
        -> visual consumers
        -> refresh/business state

For each arrow, answer:

| Participant | Owns | Must not own |
|---|---|---|
| LazyColumn, Column, or pager | content movement and its scroll state | refresh business state |
| Top-app-bar behavior | title height/offset and bar scroll response | list position |
| PullToRefresh | pull gesture, indicator state, threshold, and refresh callback | the screen's loading result |
| overScrollVertical | boundary displacement and spring lifecycle | the logical refresh boolean |
| band/tab/custom visual | a derived visual response | a second gesture connection |
| ViewModel/screen owner | data loading and persistent business state | pixel-level layout animation |

If one implementation owns two rows in this table, verify that it is deliberate and source-supported. If two implementations own the same row, expect competing consumption or duplicated state.

## Choose one top-bar integration path

For Brownfield work, keep the topology already owned by the page helper unless the user explicitly requests a migration or the existing chain is proven defective. Path A is a new integration option; Path B is the default when the target already has a list modifier helper. Do not switch paths merely because Path A has a convenient parameter.

### Path A: Miuix-integrated PullToRefresh

Use this when the target version's PullToRefresh contract supports the optional top-app-bar behavior and the page does not already have a shared wrapper that owns the chain:

    val scrollBehavior = MiuixScrollBehavior()

    Scaffold(
        topBar = {
            TopAppBar(
                title = "Page",
                scrollBehavior = scrollBehavior,
            )
        },
    ) { padding ->
        PullToRefresh(
            isRefreshing = isRefreshing,
            onRefresh = onRefresh,
            topAppBarScrollBehavior = scrollBehavior,
            contentPadding = padding,
        ) {
            LazyColumn(
                contentPadding = padding,
            ) {
                // Content
            }
        }
    }

In this topology, PullToRefresh installs the coordinated connection. Do not also attach the same top-bar connection to the inner LazyColumn merely because the basic collapsing-bar recipe shows nestedScroll. Verify the target source and compile if the project adds another wrapper.

### Path B: Example or existing consumer-owned page helper

Use this when a mature project already has a page-scroll helper that owns the primary list's modifier chain and needs to expose damped overscroll to application visuals. The current Miuix Example's PullToRefreshPage uses this topology: PullToRefresh omits topAppBarScrollBehavior, while the inner LazyColumn's pageScrollModifiers owns the top-bar connection and the overScrollVertical callback. Its source comment explicitly prevents feeding the app-bar connection twice. This is strong integrated-example evidence, not a universal requirement for every project.

A schematic shape is:

    primary scroll owner
        -> overScrollVertical(onOffsetChanged = update visual overscroll)
        -> the one existing top-bar nested-scroll connection

The Example helper updates the current source's TopAppBarState.overscrollOffset. A different project may use an application-owned visual channel, but the callback must receive the spring and reset frames; do not update the value only during the finger drag and leave stale displacement after release.

If the page also wraps that list in PullToRefresh, determine whether the wrapper uses Path A or whether the existing helper intentionally keeps the top-bar connection on the list. Do not combine both paths without tracing the source. The fact that the list helper is called pageScrollModifiers does not make its order portable to another project.

Treat an explicit overScrollVertical modifier and the theme-provided scrollable overscroll effect as separate mechanisms. If the target's demo or source uses overscrollEffect = null to make the explicit modifier the only visual effect, follow that contract; if the existing helper relies on the theme effect, preserve it until the actual chain is verified.

## Pull-to-refresh is not only a boolean

Keep these channels separate:

- isRefreshing: hoisted logical loading state, normally owned by the screen or ViewModel;
- PullToRefreshState: indicator, threshold, drag, and completion animation state;
- onPullProgress: refresh gesture progress when another visual needs the refresh range;
- overscroll offset: boundary displacement and spring motion for app-bar/band/tab visuals;
- TopAppBarState: title collapse and scroll-dependent visual state.

Do not derive a second refresh state from overscroll pixels. Do not treat pullProgress equal to one as a universal trigger; use the component's documented state/threshold contract for the target version. Do not describe PullToRefresh as a passive indicator: it installs gesture behavior and participates in nested scroll.

## Visual bridge rules

An explicit bridge is appropriate when a visual outside the overscroll modifier must react to the same damped motion, for example a top-bar band, a segmented tab surface, or a custom elastic header:

1. Create one stable bridge callback for the existing state object.
2. Feed it from the overscroll component's onOffsetChanged callback.
3. Consume the value only as visual input.
4. Let the source callback reset it to zero and include spring frames.
5. Keep list position, refresh loading, and route state in their existing owners.

Do not add a second TopAppBarState only to transport the visual offset. Do not manually translate the list and the bar independently from the same pointer event. Do not use a callback that reports only raw drag distance when the component's spring animation is part of the intended visual.

## Common conflict patterns

| Symptom | Likely cause | First check |
|---|---|---|
| Bar collapses twice or skips positions | The same behavior connection is attached through two paths | PullToRefresh arguments, outer wrappers, and list modifiers |
| Refresh consumes a bar gesture unexpectedly | The chain order changes with refresh state | Current PullToRefresh source and whether the behavior is passed to it |
| Bar/band snaps while content rebounds | No explicit damped-offset bridge, or it is reset too early | overScrollVertical callback and its visual consumer |
| Indicator reaches refresh but content remains displaced | Refresh state and overscroll state are split | PullToRefreshState, LocalOverScrollState, and the page helper |
| Pager and tab/list gestures fight | Horizontal and vertical ownership is implicit | Miuix pager gesture utilities and the actual nested lists |
| Rebound works by touch but not wheel/keyboard | The effect is press/pan scoped | Source contract and platform input path |

Do not fix these symptoms by adding another nestedScroll modifier until the current chain is mapped. A new connection can hide the real owner conflict while making fling and rebound behavior less deterministic.

## Validation matrix

For a page with this interaction, validate the states that apply:

- normal upward scroll and top-bar collapse;
- downward scroll and top-bar expansion;
- top-boundary pull below threshold, threshold crossing, release, and cancellation;
- active refresh, completion, and a second gesture during completion;
- overscroll spring frames and the final zero callback;
- band/tab/header visual progress during both drag and rebound;
- bottom-boundary overscroll and ordinary fling;
- resize, compact/wide shell, IME, and nested page host if present;
- wheel/keyboard or pointer input on supported desktop targets;
- the exact project Miuix version, affected-target compilation, and runtime/device behavior for gesture claims.

Compilation proves that the selected calls type-check. It does not prove nested-scroll ordering, spring synchronization, or the absence of gesture conflicts on a device.
