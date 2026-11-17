# Content and Overlay Compositions

Use when the page needs grouped content, a form/detail body, a page-scoped dialog/sheet/menu, or a decision about which host should own a transient surface.

Do not use this guide to choose an exact Miuix overload. Hand exact symbols to miuix-components. If the main question is a scroll/refresh/overscroll chain, use [Gesture dynamics](gesture-dynamics.md) first.

Keep content semantics, state ownership, and surface lifetime separate. Grouping is a visual and semantic choice; Overlay versus Window is a lifecycle/host decision.

Before implementation, verify exact component signatures and host/lifecycle parameters through miuix-components. After implementation, check state restoration, dismissal and exit animation, IME/insets when relevant, and the target's compiled and rendered behavior separately.

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
        Card(
            modifier = Modifier
                .fillMaxWidth()
                .padding(horizontal = 12.dp)
                .padding(bottom = 12.dp)
        ) {
            // Related rows or controls.
        }
    }
}
```

For this design specification, the grouped Card uses `12.dp` external horizontal inset and `12.dp` bottom gap. `Card` is still a containment choice, not a page requirement: do not wrap every independent row or every page in a card. Preference rows are appropriate when the content is a preference interaction; custom content is appropriate when the semantics are not preferences.

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

Keep the dialog call in composition while its `show` value changes to `false` so the component can run its own exit animation. Use `onDismissFinished` for state cleanup that must happen after the hide animation; an outer `if (!show) return` or removal of the dialog subtree cuts off that lifecycle. For input surfaces, inspect the target window's IME policy, `insideMargin`, and Android `windowSoftInputMode` together; `adjustResize` is a target-window decision, not a universal Miuix dialog parameter.

## Selection table

| Need | Default composition | Avoid |
|---|---|---|
| Pinned title over a scrollable page | `Scaffold` + `SmallTopAppBar` + one primary scroll owner | Putting the bar inside the list |
| Large title that collapses with content | `Scaffold` + `TopAppBar` + one shared `MiuixScrollBehavior` + `nestedScroll` | A behavior that is not attached to the scroll owner |
| Explicit Glass page shell | `Scaffold` + `GlassTopAppBar`/`GlassNavigationBar` + one recorded `Backdrop` | Recording the Glass surface or popup host into its own backdrop |
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
