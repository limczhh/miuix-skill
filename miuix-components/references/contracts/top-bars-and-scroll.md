# Top Bar and Scroll Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: upstream main at commit d8efe1872c08c863a8b33e7044dd2a8b43038032 (v0.9.4-80-gd8efe187, not a release tag).

Use when the task needs TopAppBar, SmallTopAppBar, BlurTopAppBar, MiuixScrollBehavior, or rememberTopAppBarState.

Do not use when the task only needs a page-level placement decision; use miuix-guidelines for that.

Before copying a call:

- Check the target project's resolved Miuix version, source set, and dependency substitution.
- Treat the declaration, package/import, module, defaults, state/callback direction, and host requirement as one contract.
- An omitted parameter is not permission to invent an overload or transfer a Material parameter.
- `catalog-verified` means the pinned declaration was checked; `target-source-verified` requires checking the target's resolved source; `compile-verified` requires compiling the affected target.

The exact source path is part of each contract below. If the target version differs, re-run the source-verification gate before presenting version-sensitive code.
## Top app bar

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`  
**Documentation:** `docs/components/basic/topappbar.md`
**Demo:** `docs/demo/src/commonMain/kotlin/TopAppBarDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.MiuixScrollBehavior
import top.yukonga.miuix.kmp.basic.BlurTopAppBar
import top.yukonga.miuix.kmp.basic.SmallTopAppBar
import top.yukonga.miuix.kmp.basic.TopAppBar
import top.yukonga.miuix.kmp.basic.rememberTopAppBarState
```

`TopAppBar`, `BlurTopAppBar`, and `SmallTopAppBar` are separate public functions. The title is a `String`, the actions use `RowScope`, and `scrollBehavior` is nullable. `BlurTopAppBar` is the current-main variant whose large title blur radius is a required parameter; it is not an extra parameter on `TopAppBar`.

```kotlin
@Composable
fun TopAppBar(
    title: String,
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surface,
    titleColor: Color = MiuixTheme.colorScheme.onSurface,
    largeTitle: String = title,
    largeTitleColor: Color = MiuixTheme.colorScheme.onSurface,
    subtitle: String = "",
    subtitleColor: Color = MiuixTheme.colorScheme.onSurfaceVariantSummary,
    navigationIcon: @Composable () -> Unit = {},
    actions: @Composable RowScope.() -> Unit = {},
    scrollBehavior: ScrollBehavior? = null,
    defaultWindowInsetsPadding: Boolean = true,
    titlePadding: Dp = TopAppBarDefaults.TitlePadding,
    navigationIconPadding: Dp = TopAppBarDefaults.NavigationIconPadding,
    actionIconPadding: Dp = TopAppBarDefaults.ActionIconPadding,
    bottomContent: @Composable () -> Unit = {},
)

@Composable
fun SmallTopAppBar(
    title: String,
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surface,
    titleColor: Color = MiuixTheme.colorScheme.onSurface,
    subtitle: String = "",
    subtitleColor: Color = MiuixTheme.colorScheme.onSurfaceVariantSummary,
    navigationIcon: @Composable () -> Unit = {},
    actions: @Composable RowScope.() -> Unit = {},
    scrollBehavior: ScrollBehavior? = null,
    defaultWindowInsetsPadding: Boolean = true,
    titlePadding: Dp = TopAppBarDefaults.TitlePadding,
    navigationIconPadding: Dp = TopAppBarDefaults.NavigationIconPadding,
    actionIconPadding: Dp = TopAppBarDefaults.ActionIconPadding,
    bottomContent: @Composable () -> Unit = {},
)

@Composable
fun BlurTopAppBar(
    title: String,
    largeTitleBlurRadius: Dp,
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surface,
    titleColor: Color = MiuixTheme.colorScheme.onSurface,
    largeTitle: String = title,
    largeTitleColor: Color = MiuixTheme.colorScheme.onSurface,
    subtitle: String = "",
    subtitleColor: Color = MiuixTheme.colorScheme.onSurfaceVariantSummary,
    navigationIcon: @Composable () -> Unit = {},
    actions: @Composable RowScope.() -> Unit = {},
    scrollBehavior: ScrollBehavior? = null,
    defaultWindowInsetsPadding: Boolean = true,
    titlePadding: Dp = TopAppBarDefaults.TitlePadding,
    navigationIconPadding: Dp = TopAppBarDefaults.NavigationIconPadding,
    actionIconPadding: Dp = TopAppBarDefaults.ActionIconPadding,
    titleAlpha: () -> Float = { 1f },
    bottomContent: @Composable () -> Unit = {},
)
```

Minimal call:

```kotlin
TopAppBar(
    title = "Page",
    subtitle = "Optional summary",
)
```

Use `SmallTopAppBar` when the bar must remain pinned. Do not pass a composable title or invent a separate `largeTitle` parameter for `SmallTopAppBar`.

Use `BlurTopAppBar` only when the large-title blur is an intentional product effect. Its required `largeTitleBlurRadius` has no default, and the same nested-scroll connection rule applies when it receives `scrollBehavior`.

## Collapsing top bar behavior

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`  
**Documentation:** `docs/components/basic/topappbar.md`, `docs/components/basic/scaffold.md`
**Demo:** `docs/demo/src/commonMain/kotlin/TopAppBarDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.MiuixScrollBehavior
import top.yukonga.miuix.kmp.basic.Scaffold
import top.yukonga.miuix.kmp.basic.TopAppBar
import top.yukonga.miuix.kmp.basic.rememberTopAppBarState
```

`MiuixScrollBehavior` returns the `ScrollBehavior` consumed by `TopAppBar` or `SmallTopAppBar`. The scrolling content must attach the same behavior's `nestedScrollConnection`; creating the behavior without attaching that connection does not make the bar collapse.

```kotlin
@Composable
fun MiuixScrollBehavior(
    state: TopAppBarState = rememberTopAppBarState(),
    canScroll: () -> Boolean = { true },
    snapAnimationSpec: AnimationSpec<Float>? = spring(stiffness = 2500f),
    flingAnimationSpec: DecayAnimationSpec<Float>? = rememberSplineBasedDecay(),
): ScrollBehavior

@Composable
fun rememberTopAppBarState(
    initialHeightOffsetLimit: Float = -Float.MAX_VALUE,
    initialHeightOffset: Float = 0f,
    initialContentOffset: Float = 0f,
): TopAppBarState
```

Minimal integration shape:

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

Use `TopAppBar` for the large-title/collapsing form. `SmallTopAppBar` is the pinned form; its source pins the shared scroll state rather than providing a second collapsing recipe. Keep the `LazyColumn`/scroll owner separate from the app bar state, and do not attach a different `nestedScrollConnection`.
