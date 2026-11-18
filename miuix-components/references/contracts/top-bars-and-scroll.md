# Top Bar and Scroll Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: release tag v0.9.5-beta01 at commit 6d2554a850b8960e2616c968263ff64beb5ffb03.

Use when the task needs TopAppBar, SmallTopAppBar, TopAppBarLayout, MiuixScrollBehavior, or rememberTopAppBarState.

Do not use when the task only needs a page-level placement decision; use miuix-guidelines for that.

Before copying a call:

- Check the target project's resolved Miuix version, source set, and dependency substitution.
- Treat the declaration, package/import, module, defaults, state/callback direction, and host requirement as one contract.
- An omitted parameter is not permission to invent an overload or transfer a Material parameter.
- `catalog-verified` means the pinned declaration was checked; `target-source-verified` requires checking the target's resolved source; `compile-verified` requires compiling the affected target.

The exact source path is part of each contract below. Confirm beta01 resolution and inspect local overrides before presenting code; another release is outside this Skill's supported scope.
## Top app bar

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`  
**Documentation:** `docs/components/basic/topappbar.md`
**Demo:** `docs/demo/src/commonMain/kotlin/TopAppBarDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.MiuixScrollBehavior
import top.yukonga.miuix.kmp.basic.SmallTopAppBar
import top.yukonga.miuix.kmp.basic.TopAppBar
import top.yukonga.miuix.kmp.basic.rememberTopAppBarState
```

`TopAppBar` and `SmallTopAppBar` take a `String` title and nullable `ScrollBehavior`; actions use `RowScope`. In beta01 `TopAppBar(expandable = false)` keeps a compact title visible even at rest. `SmallTopAppBar` remains a distinct pinned variant; do not assume identical initial title visibility.

```kotlin
@Composable
fun TopAppBar(
    title: String,
    modifier: Modifier = Modifier,
    expandable: Boolean = true,
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

```

Minimal call:

```kotlin
TopAppBar(
    title = "Page",
    subtitle = "Optional summary",
)
```

Use `SmallTopAppBar` when the bar must remain pinned. Do not pass a composable title or invent a separate `largeTitle` parameter for `SmallTopAppBar`.

For title-transition blur use the public low-level `TopAppBarLayout(titleBlurRadius = ...)` after verifying its full required arguments, or choose `GlassTopAppBar` for an intentional Glass treatment. This differs from backdrop blur behind an ordinary bar.

### Top inset and background behavior

In the pinned `TopAppBar.kt`, both ordinary layout implementations apply `.background(color)` before the internal `.windowInsetsPadding(WindowInsets.systemBars.only(WindowInsetsSides.Top))`. The background therefore includes the top inset area when the bar owns that area; its foreground is inset. A caller-applied outer padding can still move the entire bar below that region.

`defaultWindowInsetsPadding` only gates the default **horizontal** display-cutout/navigation-bar modifiers in beta01. Setting it to false leaves the internal top-system-bar inset modifier active. That modifier respects already-consumed insets; a parent/shared wrapper that owns safe-area handling should consume what it handles rather than adding an unconsumed duplicate gap. Caption/custom safe-area handling belongs to the actual application/window contract.

For Glass, `modifier` belongs to the outer background/foreground layout and `contentModifier` belongs to the inner title/control layout. The inner layout uses the same top-inset path. The progressive band accounts for `WindowInsets.statusBars`; the non-progressive band follows measured foreground height plus overhang. Keep foreground safe padding separate from the background origin. The [composition guide](../../../miuix-guidelines/references/top-bar-compositions.md#render-behind-the-status-bar) owns the application placement decision.

## Low-level title layout

`TopAppBarLayout` is public in beta01 and is the route for a custom title blur without adopting Glass. It is a layout API with required colors, slots, paddings and nullable `scrollBehavior`; `TopAppBar` remains the smaller ordinary-page entry point. Import `top.yukonga.miuix.kmp.basic.TopAppBarLayout`.

```kotlin
@Composable
fun TopAppBarLayout(
    title: String,
    color: Color,
    titleColor: Color,
    largeTitleColor: Color,
    subtitle: String,
    subtitleColor: Color,
    navigationIcon: @Composable () -> Unit,
    actions: @Composable () -> Unit,
    titlePadding: Dp,
    navigationIconPadding: Dp,
    actionIconPadding: Dp,
    scrollBehavior: ScrollBehavior?,
    modifier: Modifier = Modifier,
    expandable: Boolean = true,
    largeTitle: String = title,
    titleBlurRadius: Dp = 0.dp,
    defaultWindowInsetsPadding: Boolean = true,
    titleAlpha: () -> Float = { 1f },
    bottomContent: @Composable () -> Unit = {},
)
```

Read `titleAlpha` in its deferred callback; it is not a saved title state or background opacity. `titleBlurRadius` affects titles during transition, not the sampled scene behind the bar.

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
