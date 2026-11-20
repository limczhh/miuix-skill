# Surface and Navigation Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta02 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

## Card and section title

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Card.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/SmallTitle.kt`  
**Documentation:** `docs/components/basic/card.md`, `docs/components/basic/smalltitle.md`
**Demo:** `docs/demo/src/commonMain/kotlin/CardDemo.kt`, `SmallTitleDemo.kt`

`Card` has a non-clickable overload and a clickable/long-press overload. The two overloads share the first parameters; use named arguments when selecting the interactive form.

Read the pinned documentation/source: [Card / Card](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/card.md), [SmallTitle](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/smalltitle.md).

Pinned defaults that are safe to quote for this version:

- `CardDefaults.CornerRadius`: `16.dp`
- `CardDefaults.InsideMargin`: `PaddingValues(0.dp)`
- `SmallTitleDefaults.InsideMargin`: `PaddingValues(28.dp, 8.dp)`

These defaults do not make `Card` mandatory for every page. Select it only when the content has a real containment relationship.

## Navigation bar

**Module:** `miuix-ui` (icons may additionally require `miuix-icons`)  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt`  
**Documentation:** `docs/components/basic/navigationbar.md`
**Demo:** `docs/demo/src/commonMain/kotlin/NavigationBarDemo.kt`

The source documents `NavigationBar` for 2–5 items. `NavigationBarItem` is a `RowScope` extension, so it must be called inside the bar content lambda.

Read the pinned documentation/source: [NavigationBar / RowScope.NavigationBarItem](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/navigationbar.md).

`itemIcon` must be an actual `ImageVector`; do not replace it with a Material icon unless that dependency and icon choice are intentional. The `colors` parameter appears before `badge`; name `badge` instead of relying on positional arguments. The navigation bar presents the route owner's state; it must not create a second back stack.

## Floating navigation bar

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt`  
**Documentation:** `docs/components/basic/navigationbar.md`
**Demo:** `docs/demo/src/commonMain/kotlin/NavigationBarDemo.kt`

`FloatingNavigationBar` is still bottom navigation, not a generic overlay. Put it in the owning `Scaffold`'s `bottomBar` slot so the page content receives the correct bottom `PaddingValues`. The documented navigation pattern supports two to five icon items. In beta02, `defaultWindowInsetsPadding = true` applies horizontal display-cutout/navigation-bar insets to the group and combines navigation-bar and animated caption-bar values for its own bottom spacing; disabling it transfers both horizontal and component-owned bottom inset responsibility to the caller. It does not mean that every surrounding bottom inset is transferred to the caller.

Read the pinned documentation/source: [FloatingNavigationBar / FloatingNavigationBarItem](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/navigationbar.md).

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

The public parameter spelling is `horizontalOutSidePadding` with a capital `S`; do not silently change it to `horizontalOutsidePadding`. Its outside padding is applied by the source when the alignment is `Alignment.Start` or `Alignment.End`; centered content does not use that side padding. If `defaultWindowInsetsPadding` is disabled, the caller owns the cutout, navigation-bar and caption-bar inset contract and the component contributes no bottom spacing. Do not manually overlay the bar on a scrolling list unless that overlap is an intentional product decision.
