# Surface and Navigation Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: upstream main at commit d8efe1872c08c863a8b33e7044dd2a8b43038032 (v0.9.4-80-gd8efe187, not a release tag).

Use when the task needs Card, SmallTitle, NavigationBar, or FloatingNavigationBar.

Do not use when the task only needs to decide whether navigation belongs to the shell or page; use miuix-guidelines for that.

Before copying a call:

- Check the target project's resolved Miuix version, source set, and dependency substitution.
- Treat the declaration, package/import, module, defaults, state/callback direction, and host requirement as one contract.
- An omitted parameter is not permission to invent an overload or transfer a Material parameter.
- `catalog-verified` means the pinned declaration was checked; `target-source-verified` requires checking the target's resolved source; `compile-verified` requires compiling the affected target.

The exact source path is part of each contract below. If the target version differs, re-run the source-verification gate before presenting version-sensitive code.
## Card and section title

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Card.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/SmallTitle.kt`  
**Documentation:** `docs/components/basic/card.md`, `docs/components/basic/smalltitle.md`
**Demo:** `docs/demo/src/commonMain/kotlin/CardDemo.kt`, `SmallTitleDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.Card
import top.yukonga.miuix.kmp.basic.SmallTitle
import top.yukonga.miuix.kmp.utils.PressFeedbackType
```

`Card` has a non-clickable overload and a clickable/long-press overload. The two overloads share the first parameters; use named arguments when selecting the interactive form.

```kotlin
@Composable
fun Card(
    modifier: Modifier = Modifier,
    cornerRadius: Dp = CardDefaults.CornerRadius,
    insideMargin: PaddingValues = CardDefaults.InsideMargin,
    colors: CardColors = CardDefaults.defaultColors(),
    content: @Composable ColumnScope.() -> Unit,
)

@Composable
fun Card(
    modifier: Modifier = Modifier,
    cornerRadius: Dp = CardDefaults.CornerRadius,
    insideMargin: PaddingValues = CardDefaults.InsideMargin,
    colors: CardColors = CardDefaults.defaultColors(),
    pressFeedbackType: PressFeedbackType = PressFeedbackType.None,
    showIndication: Boolean = false,
    holdDownState: Boolean = false,
    onClick: (() -> Unit)? = null,
    onLongPress: (() -> Unit)? = null,
    content: @Composable ColumnScope.() -> Unit,
)

@Composable
fun SmallTitle(
    text: String,
    modifier: Modifier = Modifier,
    textColor: Color = MiuixTheme.colorScheme.onBackgroundVariant,
    insideMargin: PaddingValues = SmallTitleDefaults.InsideMargin,
)
```

Pinned defaults that are safe to quote for this version:

- `CardDefaults.CornerRadius`: `16.dp`
- `CardDefaults.InsideMargin`: `PaddingValues(0.dp)`
- `SmallTitleDefaults.InsideMargin`: `PaddingValues(28.dp, 8.dp)`

Minimal grouping call:

```kotlin
SmallTitle(text = "Group")
Card {
    // Related content.
}
```

These defaults do not make `Card` mandatory for every page. Select it only when the content has a real containment relationship.

## Navigation bar

**Module:** `miuix-ui` (icons may additionally require `miuix-icons`)  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt`  
**Documentation:** `docs/components/basic/navigationbar.md`
**Demo:** `docs/demo/src/commonMain/kotlin/NavigationBarDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.NavigationBar
import top.yukonga.miuix.kmp.basic.NavigationBarDefaults
import top.yukonga.miuix.kmp.basic.NavigationBarDisplayMode
import top.yukonga.miuix.kmp.basic.NavigationBarItem
```

The source documents `NavigationBar` for 2–5 items. `NavigationBarItem` is a `RowScope` extension, so it must be called inside the bar content lambda.

```kotlin
@Composable
fun NavigationBar(
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surface,
    showDivider: Boolean = true,
    defaultWindowInsetsPadding: Boolean = true,
    mode: NavigationBarDisplayMode = NavigationBarDisplayMode.IconAndText,
    content: @Composable RowScope.() -> Unit,
)

@Composable
fun RowScope.NavigationBarItem(
    selected: Boolean,
    onClick: () -> Unit,
    icon: ImageVector,
    label: String,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    colors: NavigationBarItemColors = NavigationBarDefaults.navigationBarItemColors(),
    badge: (@Composable () -> Unit)? = null,
)
```

Minimal call shape:

```kotlin
NavigationBar {
    NavigationBarItem(
        selected = selectedIndex == 0,
        onClick = { selectedIndex = 0 },
        icon = itemIcon,
        label = "Home",
    )
}
```

`itemIcon` must be an actual `ImageVector`; do not replace it with a Material icon unless that dependency and icon choice are intentional. The `colors` parameter appears before `badge`; name `badge` instead of relying on positional arguments. The navigation bar presents the route owner's state; it must not create a second back stack.

## Floating navigation bar

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt`  
**Documentation:** `docs/components/basic/navigationbar.md`
**Demo:** `docs/demo/src/commonMain/kotlin/NavigationBarDemo.kt`

`FloatingNavigationBar` is still bottom navigation, not a generic overlay. Put it in the owning `Scaffold`'s `bottomBar` slot so the page content receives the correct bottom `PaddingValues`. The documented navigation pattern supports two to five icon items. The component always creates its own floating bottom spacing; `defaultWindowInsetsPadding = true` additionally applies the animated caption-bar inset. It does not mean that every surrounding bottom inset is transferred to the caller.

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.FloatingNavigationBar
import top.yukonga.miuix.kmp.basic.FloatingNavigationBarDefaults
import top.yukonga.miuix.kmp.basic.FloatingNavigationBarItem
import top.yukonga.miuix.kmp.basic.NavigationBarDefaults
import top.yukonga.miuix.kmp.basic.NavigationBarItemColors
import top.yukonga.miuix.kmp.basic.Scaffold
```

```kotlin
@Composable
fun FloatingNavigationBar(
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surfaceContainer,
    cornerRadius: Dp = FloatingToolbarDefaults.CornerRadius,
    horizontalAlignment: Alignment.Horizontal = CenterHorizontally,
    horizontalOutSidePadding: Dp = FloatingNavigationBarDefaults.HorizontalOutSidePadding,
    shadowElevation: Dp = FloatingNavigationBarDefaults.ShadowElevation,
    showDivider: Boolean = false,
    defaultWindowInsetsPadding: Boolean = true,
    content: @Composable () -> Unit,
)

@Composable
fun FloatingNavigationBarItem(
    selected: Boolean,
    onClick: () -> Unit,
    icon: ImageVector,
    label: String,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    colors: NavigationBarItemColors = NavigationBarDefaults.navigationBarItemColors(),
    badge: (@Composable () -> Unit)? = null,
)
```

Minimal host shape:

```kotlin
Scaffold(
    bottomBar = {
        FloatingNavigationBar {
            destinations.forEachIndexed { index, destination ->
                FloatingNavigationBarItem(
                    selected = selectedIndex == index,
                    onClick = { onDestinationClick(index) },
                    icon = destination.icon,
                    label = destination.label,
                )
            }
        }
    },
) { innerPadding ->
    // Apply innerPadding once at the primary content boundary.
}
```

The public parameter spelling is `horizontalOutSidePadding` with a capital `S`; do not silently change it to `horizontalOutsidePadding`. Its outside padding is applied by the source when the alignment is `Alignment.Start` or `Alignment.End`; centered content does not use that side padding. If `defaultWindowInsetsPadding` is disabled, the caller owns the caption-bar inset contract, but the component's own floating bottom spacing remains. Do not manually overlay the bar on a scrolling list unless that overlap is an intentional product decision.
