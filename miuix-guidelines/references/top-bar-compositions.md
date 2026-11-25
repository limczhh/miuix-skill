# Top Bar Composition Guide

Use this guide to implement the [standard top-app-bar rules](rules/page-shell.md#standard-top-app-bar). The normal scrollable-page composition integrates material and motion together. It is not a menu of equally preferred bare, blurred, and disconnected bars.

For an existing host/helper, start with [Brownfield evolution](brownfield-evolution.md). For refresh, pager, or rebound interactions, use [Gesture dynamics](gesture-dynamics.md) to select the connection owner.

## Standard standalone scroll page

The relationship is:

```text
page owner: window policy + scroll behavior + backdrop policy + effective bar color
└── Scaffold
    ├── topBar: full background frame(including owned top safe area)
    │   ├── blur / fallback background
    │   └── adaptive foreground bar(safe content, same behavior)
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
        modifier = (if (backdrop != null) Modifier.layerBackdrop(backdrop) else Modifier)
            .background(MiuixTheme.colorScheme.surface),
    ) {
        LazyColumn(
            state = listState,
            modifier = Modifier
                .fillMaxSize()
                .consumeWindowInsets(contentPadding)
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

The example is the standalone case from [inset rules](rules/page-shell.md#insets-and-content-padding). The helper merges current safe areas; a shell-hosted page additionally supplies its outer handoff and reserved-start policy. The list applies the effective padding through `contentPadding` and consumes that same value through `consumeWindowInsets`; consumption does not add another gap or move the recorded background below the bar. If an existing wrapper already owns consumption, keep it there instead of consuming again. Standard refresh content retains list connection ownership. The adaptive helper calls `TopAppBar(expandable = !isWideWindow)` with the same behavior, and the list helper bridges damped `onOffsetChanged` to `state.overscrollOffset` for rebound-driven visuals. No version-compatibility branch, reflection or duplicate controller is needed.

## Render behind the status bar

Apply the [status-bar background rule](rules/page-shell.md#status-bar-background-ownership) to the helper implementation as well as its call site. Reserving room for system icons and rendering behind them are separate responsibilities:

```text
window top / edge-to-edge top-bar frame
├── background: status-bar area + measured title/actions/tabs area
└── foreground: consume top safe inset → title/actions/tabs
```

For the ordinary helper above, keep the outer `BlurredBar` frame unpadded at the owned top edge. Its `matchParentSize` background then includes the inset space measured with the foreground. The foreground bar's own inset handling, or an application helper that applies and consumes the remaining safe insets, keeps controls clear. When blur is off, paint `barColor` over the same full frame. Do not put `statusBarsPadding()` on the whole wrapper and assume a child background will fill the excluded strip, or size the background to the collapsed control-row height alone.

The pinned rc01 `TopAppBarLayout` and `SmallTopAppBarLayout` apply their `background(color)` **before** `windowInsetsPadding(WindowInsets.systemBars.only(WindowInsetsSides.Top))`. Their `defaultWindowInsetsPadding` flag controls extra horizontal display-cutout/navigation-bar padding; the top modifier remains even when that flag is false. When a rail capsule or other center overlay can cover the title, pass its start coordinate through `centerObstructionStart` instead of layering a second title or inventing a width workaround. If a shared helper owns top status/caption/cutout clearance, apply and consume the relevant insets so the inner bar sees only the remainder. Ordinary additive padding does not establish consumption. See the [exact inset contract](../../miuix-components/references/contracts/top-bars-and-scroll.md#top-inset-and-background-behavior).

`GlassTopAppBar` keeps its background and foreground separate. Its progressive-band height includes the status-bar inset, collapsed bar and bottom-content height; its non-progressive band uses the measured foreground height plus gradient overhang. Both layers start at the outer layout's top. In the Example-style wrapper, pass foreground safe-area handling through **`contentModifier`**, preserving the outer band's origin. Do not add the ordinary blur wrapper or an opaque status-bar cap around it. A band that fades at rest is intentional; keep the underlying page scene/background readable rather than treating every transparent pixel as a missing fill.

For an existing shell that has already reserved the top region, inspect that shell's background and inset handoff before changing page modifiers. Verify the resulting composition with visible content moving behind the status bar: expanded/collapsed title, rebound, blur on/off, light/dark theme, cutout/landscape and window caption. Source alignment does not prove the rendered pixels or system-icon contrast.

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
