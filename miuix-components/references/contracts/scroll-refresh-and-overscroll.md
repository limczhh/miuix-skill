# Scroll, Pull-to-Refresh, and Overscroll Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta01 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

## Public declarations

**Module:** `miuix-ui`  
**Sources:**

- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/PullToRefresh.kt`
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/Overscroll.kt`
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`
- `docs/guide/utils.md`

Read the pinned documentation/source: [PullToRefresh / rememberPullToRefreshState](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/basic/pulltorefresh.md), [Modifier.overScrollVertical](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/guide/utils.md).

`TopAppBarState` exposes the visual overscroll bridge used by the official utility guide:

```kotlin
var overscrollOffset: Float
val visualContentOffset: Float
```

`overscrollOffset` is a temporary damped displacement in pixels. It contributes to `visualContentOffset`; it does not replace `contentOffset`, title collapse state, list position, or refresh state.

The explicit overscroll modifiers use `onOffsetChanged`; they do not accept a `nestedScrollToParent` flag. Inspect a custom dispatch chain before adding connections. `overScrollHorizontal` and `overScrollOutOfBound` expose the same callback boundary.

## State and callback contract

- `isRefreshing` is the hoisted business/loading state. `onRefresh` starts the load and should set it to `true`; the caller eventually sets it back to `false`.
- `PullToRefreshState` owns indicator, drag, threshold, nested-scroll, and completion-animation state. Do not derive a second refresh state from overscroll pixels.
- `rememberPullToRefreshState(refreshThreshold = ...)` controls the trigger threshold. The default is `0.25f` of the full damped drag range.
- `onPullProgress` observes `fullDragProgress`, a full-range visual progress from `0f` to `1f`. It is not the same as threshold-relative `pullProgress`; it can continue changing after the refresh threshold is reached and also runs during programmatic expansion/completion animation.
- `overScrollVertical(onOffsetChanged = ...)` reports damped boundary displacement, including spring frames and the reset to zero. It is not the consumed list scroll distance and does not represent wheel or keyboard scrolling in the current implementation.

## Choose one top-bar integration topology

The current source supports two valid shapes. Choose based on the target project's existing ownership; do not combine them by habit.

### Path A: PullToRefresh owns the coordinated chain

Use when the target version supports `topAppBarScrollBehavior` and the page does not already have a list helper that owns the app-bar connection:

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
    PullToRefresh(
        isRefreshing = isRefreshing,
        onRefresh = onRefresh,
        contentPadding = innerPadding,
        topAppBarScrollBehavior = scrollBehavior,
    ) {
        LazyColumn(
            contentPadding = innerPadding,
        ) {
            // Content. Do not attach scrollBehavior.nestedScrollConnection again here.
        }
    }
}
```

In this topology, the current `PullToRefresh` implementation installs a nested-scroll connection that coordinates the supplied app-bar connection with refresh and overscroll. Attaching the same app-bar connection to the inner list as well creates a duplicate path.

### Path B: The primary list/helper owns the app-bar bridge

Use when the target already has a shared page-scroll modifier or when the list must explicitly bridge damped overscroll into the app-bar visuals:

```kotlin
val scrollBehavior = MiuixScrollBehavior()
val state = scrollBehavior.state
val onOverscrollChanged: (Float) -> Unit = remember(state) {
    { state.overscrollOffset = it }
}

PullToRefresh(
    isRefreshing = isRefreshing,
    onRefresh = onRefresh,
    contentPadding = contentPadding,
    // Leave topAppBarScrollBehavior null: the list owns this connection.
) {
    LazyColumn(
        modifier = Modifier
            .overScrollVertical(onOffsetChanged = onOverscrollChanged)
            .nestedScroll(scrollBehavior.nestedScrollConnection),
        contentPadding = contentPadding,
    ) {
        // Content.
    }
}
```

The current Miuix utility guide places the app-bar connection after `overScrollVertical`. The official Example uses this topology through its `pageScrollModifiers` helper: `PullToRefresh` does not receive the app-bar behavior, while the inner list helper owns both the overscroll bridge and the app-bar connection. This is verified integrated-example evidence, not a universal requirement for every application.

## Do not conflate these mechanisms

| Mechanism | Owns | Do not use it as |
|---|---|---|
| `LazyColumn`/primary scroll owner | Actual content movement and list position | Refresh business state |
| `MiuixScrollBehavior` | App-bar height/content scroll response through nested scroll | The list's `LazyListState` |
| `PullToRefresh` | Pull gesture, indicator, threshold, and refresh callback | A passive refresh indicator |
| `PullToRefreshState` | Refresh indicator and gesture animation state | The screen's loading result |
| `overScrollVertical` | Boundary displacement and spring lifecycle | Logical refresh state or content offset |
| `TopAppBarState.overscrollOffset` | Damped visual displacement for bars/tabs/bands | Title collapse or saved scroll position |

The explicit `overScrollVertical` modifier and the theme-provided Compose overscroll factory are separate mechanisms. If the target uses the explicit modifier as the only visual overscroll effect, follow the target's documented `overscrollEffect = null` boundary; do not disable or duplicate the theme effect without checking the actual source and existing helper.

## Verification boundary

Before changing a mixed gesture chain, inspect the target's `PullToRefresh` declaration and all callers of its page-scroll helper. Verify that only one path owns the app-bar connection, that any visual consumer receives the damped offset until it resets to zero, and that refresh completion remains driven by `isRefreshing`. Compile the affected target, then separately test drag, release/rebound, refresh threshold, refresh completion, title collapse, tab/band response, and nested-page restoration on the target device when those behaviors matter.
