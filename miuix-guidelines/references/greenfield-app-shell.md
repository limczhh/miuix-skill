# Greenfield App Shell

Use this guide when a request describes a new app with several top-level pages, a bottom destination bar, or a floating navigation bar without an existing shell. It is the default first-pass composition for requests such as “open the app to several pages with a floating bottom navigation”. It gives the agent one complete boundary to implement before page-specific content is designed.

The guide is structural. Read the selected Miuix component documentation through `miuix-components` before finalizing imports, overloads, Defaults, blur arguments, or version-sensitive flags.

## Default architecture

For two to five peer destinations, start with one shell-owned selection coordinator and one `HorizontalPager`.

```text
AppTheme
└── AppRoot
    └── NavDisplay<Route>                         route stack owner
        ├── Route.Shell
        │   └── AppShell                          persistent destination owner
        │       └── Scaffold                      one shell host
        │           ├── content
        │           │   └── HorizontalPager       one page per destination
        │           │       └── DestinationPage   full-size, usually vertical content
        │           └── bottomBar
        │               └── FloatingNavigationBar one visual navigation surface
        └── Route.Detail(...)                      pushed secondary route
            └── DetailPage                        page-owned bar and back action
```

This default has two distinct navigation responsibilities.

| Responsibility | Owner | State and operation |
|---|---|---|
| Switch peer destinations | `AppShell` | `selectedIndex` and one `PagerState` |
| Open and close details or subflows | `AppRoot` and `NavDisplay` | typed `Route` values and one back stack |
| Draw the persistent bar | `AppShell`'s `Scaffold.bottomBar` | the same `selectedIndex` and callbacks |
| Draw a secondary page | the matching `NavDisplay` entry | route arguments, page state, and `onBack` |

The destination bar selects a peer page. Its click callback does not push a route. A page action that opens a detail pushes a typed route. A detail page does not create a second destination bar unless the product explicitly defines a nested shell.

Each destination is a full pager page. Its body can contain a `LazyColumn` or a `Column.verticalScroll` for vertical content. Keep the pages as siblings of the pager and keep their vertical scroll owners inside their own page boundaries. Do not put all destinations in one outer `Column`, one outer `LazyColumn`, or one route entry with several unrelated page bodies.

### Relationship to the Miuix Example

The compact shell in the Example follows the same ownership chain with a few product-specific wrappers:

```text
AppContent outer Scaffold              global overlay and top-layer host
└── NavDisplay<Route>                  route/back-stack owner
    └── Route.Main → Home              shell root entry
        └── adaptive Home Scaffold     compact/wide branch
            └── CompactScreenLayout Scaffold
                ├── bottomBar → NavigationBar
                │   └── FloatingNavigationBar branch
                └── content Box
                    └── AppPager → HorizontalPager
                        └── MainPage / IconsPage / ...
                            └── page-owned Scaffold and vertical content
```

`Route.Main` in the Example corresponds to `Route.Shell` here. `MainPagerState` corresponds to the shell selection coordinator. `AppPager` corresponds to the peer-page pager. `CompactScreenLayout` places the destination bar in the shell `bottomBar`, and its floating branch leaves the surrounding `Box` unpainted. The Example's outer overlay host, adaptive rail, extra action slots, and Glass/iOS alternatives are added when the product needs those boundaries. They are not required for a first-pass three-page shell.

The Example's current `AppPager` also supplies its own pager gesture and fling configuration. The `overscrollEffect = null` line in this guide is the page-shell profile for a fixed peer-page edge. Keep it when the new product wants that profile, then verify the target pager API and any project gesture override through `miuix-components`.

## Route and selection contract

Choose this contract before writing page content.

```kotlin
@Serializable
sealed interface Route : NavKey {
    @Serializable
    data object Shell : Route

    @Serializable
    data class Detail(val id: String) : Route
}

private enum class Destination {
    Home,
    Library,
    Settings,
}
```

Use the actual app's route type and destination identities. The important boundary is that peer destination identities and stack route identities have different jobs.

- `Destination` values identify the pager pages and navigation items. They remain stable when the shell switches between ordinary, floating, or Glass presentation.
- `Route.Shell` is the valid root stack entry. It owns the persistent bottom navigation.
- `Route.Detail` carries the identity needed to restore and render the detail page. A temporary global variable must not carry required route data.
- The root `NavDisplay` owns route transitions and system back. The shell owns destination selection. There is one owner for each operation.
- A destination click updates the shell selection. It does not call `backStack.add`.
- An action inside a destination calls `backStack.add(Route.Detail(id))`. The detail entry uses the same typed route for its app-bar back action and system back path.
- Use `rememberNavBackStack<Route>(Route.Shell)` when route keys are serializable and process restoration is required. Use the in-memory factory only when losing the stack on process death is an intentional limitation.

For a shell that needs an independent history per destination, choose a route-stack architecture explicitly and document that choice. Do not combine a route push for every bottom-bar click with a pager that also represents those pages. That creates two selected-page authorities and makes back behavior ambiguous.

## Shell scaffold contract

The shell owns the full window background, the pager, the persistent navigation slot, and the shell-to-page inset handoff.

```kotlin
@Composable
fun AppRoot() {
    val backStack = rememberNavBackStack<Route>(Route.Shell)

    NavDisplay(
        backStack = backStack,
        modifier = Modifier.fillMaxSize(),
        onBack = {
            if (backStack.size > 1) {
                backStack.removeLastOrNull()
            }
        },
    ) {
        entry<Route.Shell> {
            AppShell(
                onOpenDetail = { id -> backStack.add(Route.Detail(id)) },
            )
        }
        entry<Route.Detail> { route ->
            DetailPage(
                id = route.id,
                onBack = { if (backStack.size > 1) backStack.removeLastOrNull() },
            )
        }
    }
}

@Composable
fun AppShell(
    onOpenDetail: (String) -> Unit,
) {
    val destinations = remember { Destination.entries.toList() }
    val pagerState = rememberPagerState(pageCount = { destinations.size })
    val selection = rememberShellSelection(pagerState)

    Scaffold(
        modifier = Modifier.fillMaxSize(),
        containerColor = Color.Transparent,
        contentWindowInsets = WindowInsets(0),
        bottomBar = {
            Box(modifier = Modifier.fillMaxWidth()) {
                FloatingNavigationBar(
                    // The wrapper has no background or clip. The component owns its shape.
                    color = selectionBarColor,
                    modifier = selectionBarModifier,
                ) {
                    destinations.forEachIndexed { index, destination ->
                        FloatingNavigationBarItem(
                            selected = selection.selectedIndex == index,
                            onClick = { selection.select(index) },
                            icon = destination.icon,
                            label = destination.label,
                        )
                    }
                }
            }
        },
    ) { innerPadding ->
        // The surface/backdrop fills the window. The pager receives the shell handoff once.
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(MiuixTheme.colorScheme.surface),
        ) {
            HorizontalPager(
                state = pagerState,
                modifier = Modifier
                    .fillMaxSize()
                    .padding(innerPadding)
                    .consumeWindowInsets(innerPadding),
                overscrollEffect = null,
            ) { index ->
                DestinationPage(
                    destination = destinations[index],
                    onOpenDetail = onOpenDetail,
                )
            }
        }
    }
}
```

The snippet is an application skeleton. `selectionBarColor`, `selectionBarModifier`, `Destination.icon`, and `Destination.label` represent app-owned values and adapters. Verify the actual Miuix declarations before compiling.

The following shell invariants are mandatory.

- The shell has one `Scaffold` that owns `bottomBar`. A page-owned `Scaffold` is allowed for a page app bar or page-local popup host after the shell boundary is stable.
- `FloatingNavigationBar` is composed directly in the shell's `bottomBar` slot. Its surrounding `Box` may provide width and alignment. It does not paint a full-width background.
- The shell `Scaffold` uses a transparent container when the page surface is drawn by the content layer. The page surface or recorded backdrop fills the complete window, including the region behind the floating bar.
- The floating bar's own shape is the blur mask, stroke boundary, and visible surface boundary. A full-width `background`, `clip`, or opaque `surface` wrapper around the bar creates the rectangular halo that this composition is intended to avoid.
- Apply the `innerPadding` handoff once at the pager or the page's primary content boundary. Keep the page surface outside that padding so the background remains continuous behind the bar.
- Use the component's own item spacing. Do not insert a `Spacer` between navigation items unless source verification shows that the selected component has no required spacing.
- Keep `selectedIndex`, the pager, and the bar above ordinary/Glass presentation branches. Rendering one active bar family at a time keeps semantics and callbacks single-owned.

## Selection coordinator

The selection coordinator is application state. It is shared by the pager and every presentation of the destination bar.

```text
select(index):
    validate index against current destination identities
    cancel the previous request
    selectedIndex = index                 // update the indicator immediately
    isNavigating = true
    start one guarded pager request

pager currentPage changed:
    if !isNavigating:
        selectedIndex = pagerState.currentPage

request completed or cancelled:
    if this request still owns the coordinator:
        isNavigating = false
        selectedIndex = pagerState.currentPage

user takes over the pager:
    cancel the programmatic request
    isNavigating = false
    selectedIndex = pagerState.currentPage
```

The coordinator must handle repeated taps, a swipe during an animation, a reverse request such as `0 → 1 → 0`, and a destination list that changes. A cancelled old job must not release a newer request's lock. Use the existing [selection coordination pattern](navigation-compositions.md#tab-selection-and-page-switching) or an equivalent implementation. `targetPage` is a prediction and does not drive the persistent selected indicator.

The pager edge policy is local to the shell. Use `overscrollEffect = null` for this peer-page profile so a horizontal edge rebound does not expose the shell's surrounding background. Child page lists keep their own vertical overscroll and refresh behavior.

## Background and blur variants

Start with a readable opaque page surface. Add sampling only after the shell has a correct surface boundary.

```text
shell content layer: opaque page surface → records the backdrop
bottomBar slot: transparent layout wrapper → shaped navigation surface
bar effect: shaped blur/fallback or Glass material
```

For an ordinary floating bar, use the shared backdrop policy from [Blur and Glass rules](rules/page-shell.md#blur-and-glass). When sampling is active, the bar color is transparent and the blur mask matches the bar's shape. When sampling is inactive or unsupported, the bar uses its semantic `surfaceContainer` fallback. Both branches keep the same item state, hit targets, and slot placement.

For `GlassNavigationBar`, pass the shell-owned backdrop through the verified Glass API and keep its material/stroke/shadow ownership inside the Glass component. Remove an obsolete ordinary blur wrapper when the selected Glass component already owns that effect. Ordinary and Glass branches share `selectedIndex`, destination identities, callbacks, and inset policy.

## Back and route behavior

Implement the back policy in this order.

1. The active popup, search surface, or IME-owned local flow handles dismissal at its own boundary.
2. A detail or subflow route pops the root stack to `Route.Shell`.
3. At the shell root, a product that uses pager-backed destinations may switch a non-root destination to the first destination through the shell coordinator.
4. The platform handles back from the root destination according to the app's host policy.

Keep this policy in the root host and shell coordinator. Every destination page must not install a competing global back handler. A detail route may use the same navigator callback through its page app bar.

## First-pass implementation order

Implement and inspect in this order.

1. Create the typed root/detail route model and the root `NavDisplay`.
2. Add the shell `Scaffold` with a transparent container and no navigation component styling.
3. Add the full-window surface and one `HorizontalPager` with three placeholder pages. Confirm each page fills its own pager viewport and owns its vertical content.
4. Add one `FloatingNavigationBar` in `bottomBar`. Confirm the surrounding bottom-slot area stays transparent and the selected index changes synchronously.
5. Add the guarded selection coordinator and pager edge policy.
6. Add the actual page content and page-owned app bars. Apply the shell-to-page padding once.
7. Add detail route pushes, route transitions, back behavior, and only then blur or Glass material.
8. Verify compact and wide-window presentation from one adaptive policy. Preserve the same destination identities, selection, pager state, and route stack during a bar-to-rail change.

Do not begin by styling each page independently. A rendered page without the shell contract is incomplete for this request.

## Delivery contract for a first pass

When implementing this request, deliver the shell boundary in the same change as the first placeholder pages.

- Define the real root route, destination identities, shell composable, pager state, selection owner, and bottom-bar callback path. Every helper named in the implementation must be defined in the change or already exist in the target project.
- Keep placeholder page bodies intentionally small and compilable. A placeholder may be a full-size `Box` with a title or a vertical list. It must exercise the same pager, inset, and route boundary that the final page will use.
- Compile the shell before adding decorative blur, Glass material, complex cards, or feature-specific state. Fix route, host, inset, and selection errors at the shell boundary.
- Report the chosen ownership map in the handoff. Include the root route, peer-page selection owner, detail-route owner, bar host slot, page padding consumer, and background owner.
- Treat a plan, a collection of isolated page snippets, or a copied navigation component without its host and route owner as an incomplete first pass.

## Acceptance checks

Before handing off a greenfield shell, verify each item against source and the available runtime target.

- The first frame shows `Route.Shell` and one selected destination.
- Every destination is a full-size sibling page, and its body scrolls vertically inside that page when needed.
- The bottom bar and pager consume one `selectedIndex` and one callback path.
- A destination click changes the selected indicator immediately, then settles the pager without stale feedback.
- A user swipe updates the selected indicator from the settled/current pager position after the request lock is released.
- The shell background is continuous under and around the floating bar. No full-width rectangular background is painted by the bottom-bar wrapper or the default shell container.
- The bar's blur mask, fallback surface, stroke, and clipping boundary match the visible bar shape.
- Opening a detail pushes a typed route. The detail page can pop through both its app-bar action and system back.
- Back does not push a duplicate destination route, and the root stack remains valid.
- Switching ordinary, floating, Glass, or rail presentation retains destination identity, page state, and route state.
- Large text, RTL, short windows, cutouts, IME, blur-off, and unsupported-runtime fallback retain usable content and navigation hit targets.

The Example integration points for this contract are `example/shared/src/commonMain/kotlin/AppContent.kt`, `navigation/Route.kt`, `navigation/Navigator.kt`, and the page-owned `MainPage.kt`. Reuse their ownership relationships after checking the target version. Example-specific helpers and settings do not become required application APIs.
