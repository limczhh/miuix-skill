# Top Bar Composition Guide

Use this guide to implement the [standard top-app-bar rules](rules/page-shell.md#standard-top-app-bar). The normal scrollable-page composition integrates material and motion together. It is not a menu of equally preferred bare, blurred, and disconnected bars.

For an existing host/helper, start with [Brownfield evolution](brownfield-evolution.md). For refresh, pager, or rebound interactions, use [Gesture dynamics](gesture-dynamics.md) to select the connection owner.

## Standard standalone scroll page

The relationship is:

```text
page owner: window policy + scroll behavior + backdrop policy + effective bar color
└── Scaffold
    ├── topBar: blur background + adaptive foreground bar(same behavior)
    └── content: recorded backdrop layer → scroll owner(same behavior, shared safe padding)
```

This placement example uses application-owned `rememberBlurBackdrop`, `BlurredBar`, `AdaptiveTopAppBar`, `pageContentPadding`, and `pageScrollModifiers` helpers. They implement the normative material, window, inset, and scroll recipes; they are not public Miuix APIs. The blur wrapper draws a separate background below the foreground bar. Reuse matching target helpers or implement that shared boundary once, then verify actual Miuix symbols separately.

```kotlin
val backdrop = rememberBlurBackdrop()
val blurActive = backdrop != null
val barColor = if (blurActive) Color.Transparent else MiuixTheme.colorScheme.surface
val scrollBehavior = MiuixScrollBehavior()
val listState = rememberLazyListState()

Scaffold(
    topBar = {
        BlurredBar(backdrop, blurActive) {
            AdaptiveTopAppBar(
                title = title,
                color = barColor,
                scrollBehavior = scrollBehavior,
                navigationIcon = {
                    IconButton(onClick = onBack) {
                        Icon(
                            imageVector = MiuixIcons.Back,
                            contentDescription = localizedBackDescription,
                        )
                    }
                },
            )
        }
    },
) { padding ->
    val contentPadding = pageContentPadding(padding, extraBottom = 16.dp)
    Box(
        modifier = if (backdrop != null) Modifier.layerBackdrop(backdrop) else Modifier,
    ) {
        LazyColumn(
            state = listState,
            modifier = Modifier
                .fillMaxSize()
                .pageScrollModifiers(
                    enableScrollEndHaptic = true,
                    showTopAppBar = true,
                    topAppBarScrollBehavior = scrollBehavior,
                ),
            contentPadding = contentPadding,
        ) {
            // Semantic page content.
        }
    }
}
```

The example is the standalone case from [inset rules](rules/page-shell.md#insets-and-content-padding). The helper merges current safe areas; a shell-hosted page additionally supplies its outer handoff and reserved-start policy. Standard refresh content retains list connection ownership. The adaptive helper chooses a collapsing compact-window title or fixed wide-window title through the shared size policy and target-supported API. No unsupported rebound field is assumed.

## Variants to resolve deliberately

| Situation | Composition decision | Normative owner |
|---|---|---|
| Custom hero/header needs a pinned small title | Keep one visible `SmallTopAppBar` in the same material boundary; map any content reaction to the page behavior | [Standard top app bar](rules/page-shell.md#standard-top-app-bar) |
| Step-controlled or non-scrolling page | Select the fixed-header behavior appropriate to the flow and record the exception basis | [Exceptions](rules/design-system.md#exceptions-and-conformance) |
| Page-level tabs | Place the tab strip in the header boundary and coordinate it with the active pager page | [Tabs and pagers](rules/page-shell.md#tabs-and-pagers) |
| Expanded search | Resolve which input/result surface replaces the normal header presentation | [Search](rules/settings-and-overlays.md#search) |
| Pull-to-refresh | Use list ownership by default; inspect an existing wrapper-owned compatibility path before migration | [Scroll integration](rules/page-shell.md#scroll-refresh-and-haptics) |
| Explicit Glass requirement | Use the selected Glass family's host/sampling composition | [Glass guide](glass-compositions.md) |

Exact overloads/imports and component state behavior go to `miuix-components`; the snippet is placement evidence, not a compilation result. Use the [page-shell validation boundary](rules/page-shell.md#validation) for runtime checks.
