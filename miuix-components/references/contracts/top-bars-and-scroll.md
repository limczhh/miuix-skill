# Top Bar and Scroll Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta03 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

## Top app bar

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`  
**Documentation:** `docs/components/basic/topappbar.md`
**Demo:** `docs/demo/src/commonMain/kotlin/TopAppBarDemo.kt`

`TopAppBar` and `SmallTopAppBar` take a `String` title and nullable `ScrollBehavior`; actions use `RowScope`. In beta03 `TopAppBar(expandable = false)` keeps a compact title visible even at rest. `SmallTopAppBar` remains a distinct pinned variant; do not assume identical initial title visibility. Both public bar calls expose `centerObstructionStart` for a center-side overlay such as a rail capsule; the layout also uses `TopAppBarDefaults.TitleControlSpacing` (`12.dp`) when controls are present.

Read the pinned documentation/source: [TopAppBar / SmallTopAppBar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/topappbar.md).

Use `SmallTopAppBar` when the bar must remain pinned. Do not pass a composable title or invent a separate `largeTitle` parameter for `SmallTopAppBar`.

For title-transition blur use the public low-level `TopAppBarLayout(titleBlurRadius = ...)` after verifying its full required arguments, or choose `GlassTopAppBar` for an intentional Glass treatment. This differs from backdrop blur behind an ordinary bar.

### Top inset and background behavior

In the pinned `TopAppBar.kt`, both ordinary layout implementations apply `.background(color)` before the internal `.windowInsetsPadding(WindowInsets.systemBars.only(WindowInsetsSides.Top))`. The background therefore includes the top inset area when the bar owns that area; its foreground is inset. A caller-applied outer padding can still move the entire bar below that region.

`defaultWindowInsetsPadding` only gates the default **horizontal** display-cutout/navigation-bar modifiers in beta03. Setting it to false leaves the internal top-system-bar inset modifier active. That modifier respects already-consumed insets; a parent/shared wrapper that owns safe-area handling should consume what it handles rather than adding an unconsumed duplicate gap. Caption/custom safe-area handling belongs to the actual application/window contract.

For Glass, `modifier` belongs to the outer background/foreground layout and `contentModifier` belongs to the inner title/control layout. The inner layout uses the same top-inset path. In beta03 neither public `GlassTopAppBar` overload accepts `visualContentOffset`: scroll behavior reads content/negative overscroll state, explicit state reads `isContentScrolled` plus negative overscroll, and an expandable bar with a `ScrollBehavior` uses `collapsedFraction` for its band progress. The non-progressive band follows measured foreground height plus overhang. Use `centerObstructionStart` when an overlay must reserve the title's center region. Keep foreground safe padding separate from the background origin. The [composition guide](../../../miuix-guidelines/references/top-bar-compositions.md#render-behind-the-status-bar) owns the application placement decision.

## Low-level title layout

`TopAppBarLayout` is public in beta03 and is the route for a custom title blur without adopting Glass. It is a layout API with required colors, slots, paddings and nullable `scrollBehavior`; `TopAppBar` remains the smaller ordinary-page entry point. Import `top.yukonga.miuix.kmp.basic.TopAppBarLayout`.

The component doc does not describe this low-level declaration. Read [TopAppBarLayout in the pinned source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt) for its exact required arguments.

Read `titleAlpha` in its deferred callback; it is not a saved title state or background opacity. `titleBlurRadius` affects titles during transition, not the sampled scene behind the bar.

## Collapsing top bar behavior

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`  
**Documentation:** `docs/components/basic/topappbar.md`, `docs/components/basic/scaffold.md`
**Demo:** `docs/demo/src/commonMain/kotlin/TopAppBarDemo.kt`

`MiuixScrollBehavior` returns the `ScrollBehavior` consumed by `TopAppBar` or `SmallTopAppBar`. The scrolling content must attach the same behavior's `nestedScrollConnection`; creating the behavior without attaching that connection does not make the bar collapse.

Read the pinned documentation/source: [MiuixScrollBehavior / rememberTopAppBarState](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/topappbar.md).

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
