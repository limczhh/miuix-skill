# Page Composition Recipes

Use this reference when a page combines a host, app bar, navigation, scrolling content, floating actions, or overlays. It gives concrete Miuix composition shapes after ownership has been decided in [Page structure and ownership](page-structure.md). The recipes are a consistent starting point, not mandatory page types: a settings page, list page, form page, detail page, and custom-content page are all ordinary pages.

Exact Miuix signatures, imports, defaults, and version-sensitive behavior belong to the companion `miuix-components` Skill. Use the call shapes here to decide placement and ownership; verify the actual declaration in the pinned source before copying code. These snippets are composition shapes, not compile-tested files.

## Contents

- [Composition contract](#composition-contract)
- [Pinned top bar and one scroll owner](#pinned-top-bar-and-one-scroll-owner)
- [Collapsing top bar](#collapsing-top-bar)
- [Fixed bottom navigation](#fixed-bottom-navigation)
- [Floating bottom navigation](#floating-bottom-navigation)
- [Adaptive bottom navigation and rail](#adaptive-bottom-navigation-and-rail)
- [Floating action and toolbar](#floating-action-and-toolbar)
- [Grouped page content](#grouped-page-content)
- [Form or detail content](#form-or-detail-content)
- [Page-scoped overlays](#page-scoped-overlays)
- [Selection table](#selection-table)
- [Composition defects](#composition-defects)

## Composition contract

Before selecting a recipe, name these owners:

| Concern | One owner | Recipe consequence |
|---|---|---|
| Theme | Application/root shell | Do not create a page-local `MiuixTheme` just to style a page |
| Route and back stack | Application or navigation shell | Bottom/rail navigation presents selected state; it does not create a second back stack |
| Page host | The shell or page boundary that owns bars, insets, and transient layers | Use one `Scaffold` per real host boundary; nested hosts are valid only when their responsibilities are distinct |
| Primary content/scroll | The page's `LazyColumn`, `Column` with `verticalScroll`, pager, or custom layout | Keep bars and floating controls out of the scroll content |
| Scaffold/window padding | One final page content boundary | Each host may emit local `PaddingValues`; combine an intentional shell handoff with page padding before applying it once to primary content |
| Persistent state | Screen owner or model | Do not recreate selection or form state inside a bar branch |
| Transient state | The host that renders it | Keep dialog, snackbar, popup, and expanded-menu state near that host |

The normal order is:

1. Reuse the existing theme and route host.
2. Decide which shell and/or page host owns each real slot, inset, and transient layer.
3. Choose the primary content/scroll owner.
4. Place bars, navigation, floating controls, and transient layers in their owning host slots.
5. Combine any shell handoff with page-host padding intentionally, then apply the result once at the final content boundary.
6. Choose the content components by semantic role.

Do not add a `Scaffold` merely for background, grouping, or a convenient `padding` modifier. Do add one when the page owns a top/bottom bar, floating action or toolbar, snackbar/popup host, or content-window inset contract.

## Pinned top bar and one scroll owner

Use this shape when the title should remain visible while the page scrolls. `SmallTopAppBar` is the pinned app-bar form.

```kotlin
Scaffold(
    topBar = {
        SmallTopAppBar(
            title = "Page",
        )
    },
) { innerPadding ->
    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .consumeWindowInsets(innerPadding),
        contentPadding = innerPadding,
    ) {
        // Page content owns the vertical scroll.
    }
}
```

Use a `Column` with `verticalScroll` instead when the content is a small, naturally linear form. Do not put a `LazyColumn` inside that `Column` just to make one subsection scroll.

If the page has no bar, no bottom/floating slot, and no host-owned transient layer, a plain content root may be more appropriate than `Scaffold`.

## Collapsing top bar

Use this shape when a large title should collapse into the app-bar area as the primary content scrolls. The same `MiuixScrollBehavior` instance belongs to both the app bar and the scroll container.

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

The `nestedScrollConnection` is part of the behavior contract, not an optional visual tweak. Do not create one behavior for `TopAppBar` and another for `LazyColumn`. Keep `LazyListState` for list position and `TopAppBarState`/`ScrollBehavior` for app-bar motion; they are related through nested scroll, not interchangeable.

Use `SmallTopAppBar` instead when the title must remain pinned. Do not add collapsing behavior to a page whose hierarchy does not benefit from a large-title transition.

## Fixed bottom navigation

Use `NavigationBar` when compact navigation is part of the persistent shell. Keep the selected destination and destination click callback in the route/screen owner, then present them in the bar. If the shell already owns a bottom bar, a page `Scaffold` may still own its top bar; pass the shell's effective padding into the page content instead of moving navigation into the page's scroll content.

```kotlin
Scaffold(
    bottomBar = {
        NavigationBar {
            destinations.forEachIndexed { index, destination ->
                NavigationBarItem(
                    selected = selectedIndex == index,
                    onClick = { onDestinationClick(index) },
                    icon = destination.icon,
                    label = destination.label,
                )
            }
        }
    },
) { innerPadding ->
    // The primary page content consumes innerPadding once.
}
```

`NavigationBarItem` is called inside the `NavigationBar` content scope. Use it for two to five destinations as documented by the component. The bar displays route state; it must not maintain a separate page stack.

## Floating bottom navigation

Use `FloatingNavigationBar` when the same persistent bottom-navigation role should be visually separated from the window edge. It is still navigation chrome, so place it in the `Scaffold(bottomBar = ...)` belonging to the shell that owns navigation, not as an arbitrary `Box` overlay above the list.

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
    // Apply innerPadding to the primary content boundary.
}
```

The component supplies its own floating bottom spacing. Keep `defaultWindowInsetsPadding = true` unless the surrounding shell has a deliberate caption-bar inset policy; disabling it transfers only that caption-bar inset responsibility, not the component's own floating spacing. Do not compensate with unrelated hard-coded bottom padding. The public customization parameter is spelled `horizontalOutSidePadding`.

## Adaptive bottom navigation and rail

Use one navigation state and one layout policy when switching between compact and wide navigation chrome:

```text
route/screen owner
├── selected destination and navigation callbacks
└── one compact/wide policy
    ├── compact: NavigationBar or FloatingNavigationBar in Scaffold.bottomBar
    └── wide: NavigationRail in the shell's side slot
```

The active page, selected destination, and business state must survive the branch. The branch changes chrome placement, not navigation ownership. Keep the page's primary content and its inset boundary consistent across both forms; only the shell layout should change unless the available width requires an intentional content reflow.

## Floating action and toolbar

Use the `Scaffold` slots for actions that float relative to page content:

| Intent | Slot/component direction | Placement rule |
|---|---|---|
| One primary action | `floatingActionButton` with `FloatingActionButton` | Let the host position it; do not pin it inside the last list item |
| A related group of actions | `floatingToolbar` with `FloatingToolbar` | Keep the group in the host and select the appropriate toolbar position |
| A destination switcher | `bottomBar` with `NavigationBar` or `FloatingNavigationBar` | Do not model navigation as a floating action |
| A page-scoped message | `snackbarHost`/Snackbar path | Keep transient message state with the page host |

Do not use a floating toolbar as a substitute for bottom navigation, and do not make a single primary action into a toolbar just because both can float.

## Grouped page content

Use grouping when the content has a real semantic or containment relationship. A common Miuix shape is a section label followed by a card containing related rows:

```kotlin
LazyColumn(
    modifier = Modifier
        .fillMaxSize()
        .consumeWindowInsets(innerPadding),
    contentPadding = innerPadding,
) {
    item {
        SmallTitle(text = "General")
    }
    item {
        Card {
            // Related rows or controls.
        }
    }
}
```

`Card` is a containment choice, not a page requirement. Do not wrap every independent row or every page in a card. Preference rows are appropriate when the content is a preference interaction; custom content is appropriate when the semantics are not preferences.

## Form or detail content

Choose one vertical content owner for the form or detail body:

- small content: `Column` plus `verticalScroll`;
- long or repeated content: `LazyColumn`;
- horizontally paged content: a pager may own horizontal movement while each page owns its intentional vertical content.

Keep field state, validation state, and save state in the screen/model owner. Render validation beside the field that owns the error; do not move all errors into a detached snackbar or dialog. Apply the page's `PaddingValues` to the form content once, then let field-level components manage their own visual states.

## Page-scoped overlays

Use an overlay recipe when the surface belongs to the current page's host:

```text
page Scaffold
├── top/bottom/floating slots
├── primary content
└── page-scoped OverlayDialog / OverlayBottomSheet / popup
```

Keep the overlay's show/dismiss state near the page host, pass an explicit `onDismissRequest`, and let the page's `Scaffold`/popup host own its lifecycle. Choose a `WindowDialog` or `WindowBottomSheet` only when the surface must be independent of the page host or use a separate platform window. Do not replace a lifecycle decision with z-order or manual `Box` layering.

`OverlayDialog` and the related Overlay families default to `renderInRootScaffold = true` in the pinned source. If the overlay belongs to a nested page host rather than the outer shell, set `renderInRootScaffold = false` and verify that the target declaration exposes the parameter.

## Selection table

| Need | Default composition | Avoid |
|---|---|---|
| Pinned title over a scrollable page | `Scaffold` + `SmallTopAppBar` + one primary scroll owner | Putting the bar inside the list |
| Large title that collapses with content | `Scaffold` + `TopAppBar` + one shared `MiuixScrollBehavior` + `nestedScroll` | A behavior that is not attached to the scroll owner |
| Compact destination navigation | `Scaffold.bottomBar` + `NavigationBar` | A second back stack inside the bar |
| Separated/floating destination navigation | `Scaffold.bottomBar` + `FloatingNavigationBar` | Manually overlaying it over the list by default |
| Wide navigation | Shell side slot + `NavigationRail` | Recreating route state for the wide branch |
| One primary action | `Scaffold.floatingActionButton` | A button hidden in arbitrary content padding |
| Related action group | `Scaffold.floatingToolbar` | Using bottom navigation for actions |
| Grouped settings/content | `LazyColumn` + semantic `SmallTitle`/`Card` grouping | Cards around unrelated content |
| Page-owned dialog/sheet | Overlay under the intended page host | A manual z-index stack for lifecycle |

## Composition defects

Treat these as defects unless the product requirement explicitly explains them:

- a page creates a second `MiuixTheme`, route host, or navigation state;
- a top/bottom bar is part of the scrolling list instead of its host slot;
- shell/page `Scaffold` padding is ignored, applied twice, or replaced by guessed system-bar constants;
- `MiuixScrollBehavior` is created but its connection is not attached to the primary scroll owner;
- `NavigationBar`/`FloatingNavigationBar` is manually overlaid when the `bottomBar` slot would express the same role;
- compact and wide branches duplicate selected destination or business state;
- a `Window` surface is used when the surface must remain page-scoped, or an `Overlay` is used when it must outlive the page host;
- nested vertical scroll containers exist without an intentional parent-child scrolling relationship;
- a recipe is copied without checking the exact component declaration in the companion components Skill.
- nested shell/page hosts are added without distinct ownership, or a page-scoped Overlay silently resolves to the root host because its root-host flag was not reviewed;
