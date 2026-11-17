# Scroll, Pull-to-Refresh, and Overscroll Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: upstream main at commit d8efe1872c08c863a8b33e7044dd2a8b43038032 (v0.9.4-80-gd8efe187, not a release tag). The audited declarations and utility guide match the current local checkout for these files.

Use when the task needs PullToRefresh, rememberPullToRefreshState, overScrollVertical, TopAppBarState.overscrollOffset, or a nested-scroll chain that combines refresh, overscroll, and a collapsing bar.

Do not use this guide to decide the page's shell, content hierarchy, or whether a refresh interaction belongs on the page; use miuix-guidelines for those decisions first.

Before copying a call:

- Check the target project's resolved Miuix version, source set, and dependency substitution.
- Treat the declaration, state ownership, nested-scroll topology, and visual callback as one contract.
- `catalog-verified` means the pinned declaration was checked; `target-source-verified` requires checking the target's resolved source; `compile-verified` requires compiling the affected target.

## Public declarations

**Module:** `miuix-ui`  
**Sources:**

- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/PullToRefresh.kt`
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/Overscroll.kt`
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`
- `docs/guide/utils.md`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.PullToRefresh
import top.yukonga.miuix.kmp.basic.PullToRefreshState
import top.yukonga.miuix.kmp.basic.MiuixScrollBehavior
import top.yukonga.miuix.kmp.basic.ScrollBehavior
import top.yukonga.miuix.kmp.basic.Scaffold
import top.yukonga.miuix.kmp.basic.TopAppBar
import top.yukonga.miuix.kmp.basic.rememberPullToRefreshState
import top.yukonga.miuix.kmp.utils.overScrollVertical
```

Exact public declarations:

```kotlin
@Composable
fun PullToRefresh(
    isRefreshing: Boolean,
    onRefresh: () -> Unit,
    modifier: Modifier = Modifier,
    pullToRefreshState: PullToRefreshState = rememberPullToRefreshState(),
    contentPadding: PaddingValues = PaddingValues(0.dp),
    topAppBarScrollBehavior: ScrollBehavior? = null,
    color: Color = PullToRefreshDefaults.color,
    circleSize: Dp = PullToRefreshDefaults.circleSize,
    refreshTexts: List<String> = PullToRefreshDefaults.refreshTexts,
    refreshTextStyle: TextStyle = PullToRefreshDefaults.refreshTextStyle,
    onPullProgress: ((Float) -> Unit)? = null,
    content: @Composable () -> Unit,
)

@Composable
fun rememberPullToRefreshState(
    refreshThreshold: Float = 0.25f,
): PullToRefreshState

@Stable
fun Modifier.overScrollVertical(
    onOffsetChanged: ((Float) -> Unit)? = null,
    isEnabled: () -> Boolean = { true },
): Modifier
```

`TopAppBarState` exposes the visual overscroll bridge used by the official utility guide:

```kotlin
var overscrollOffset: Float
val visualContentOffset: Float
```

`overscrollOffset` is a temporary damped displacement in pixels. It contributes to `visualContentOffset`; it does not replace `contentOffset`, title collapse state, list position, or refresh state.

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
