# Example-Derived Usage Patterns

At tag `v0.9.3`, `docs/guide/best-practices.md` explicitly points to the project's Example application as a real-world demonstration of how Miuix components and design principles are applied. Treat it as a first-party reference implementation, while keeping source code authoritative for API contracts. Before applying these patterns, resolve theme and host ownership in [Project setup and theme](setup-and-theme.md) and follow the [Code Delivery Contract](../SKILL.md#code-delivery-contract).

## Contents

- [Evidence Levels](#evidence-levels)
- [Scenario to Integrated Example Map](#scenario-to-integrated-example-map)
- [Derive a Pattern Before Coding](#derive-a-pattern-before-coding)
- [Application Root and Theme](#application-root-and-theme)
- [Page Shell and Adaptive Navigation](#page-shell-and-adaptive-navigation)
- [Navigation 3 Routing](#navigation-3-routing)
- [Settings and Grouped Content](#settings-and-grouped-content)
- [State and Component Variants](#state-and-component-variants)
- [Overlay and Window Components](#overlay-and-window-components)

## Evidence Levels

| Evidence | Role | How strongly to follow it |
|---|---|---|
| Component source and defaults | Exact parameters, behavior, host requirements, tokens | Contract; verify before writing uncertain or version-sensitive code |
| Component doc | Intended purpose, supported states, conceptual guidance | Primary explanation; confirm stale or ambiguous details in source |
| `docs/demo/<Component>Demo.kt` | Minimal runnable call shape and basic states | Starting point for a single component, not a complete page template |
| `example/shared/` | Integrated page hierarchy, state flow, grouping, navigation, and adaptive layout | Preferred composition evidence; adapt it to the target app |
| Example-only effects and helpers | Showcase visuals and application infrastructure | Optional; do not present them as library requirements |

## Scenario to Integrated Example Map

Use this map after choosing components. Unless a path starts with another root, resolve it under `example/shared/src/commonMain/kotlin/`. It identifies first-party integration evidence; it does not replace the component doc, demo, or source contract.

| Scenario | Start with | Extract |
|---|---|---|
| Application theme and global UI state | `App.kt`, `AppState.kt`, `ui/Theme.kt` | Root theme, theme inputs, and stable cross-screen state |
| Adaptive app shell and navigation | `AppContent.kt`, `AppState.kt` | Compact/wide branching, Scaffold placement, NavigationBar/Rail, and shared controls |
| Navigation 3 routing and scenes | `App.kt`, `AppState.kt`, `AppContent.kt`, then `docs/guide/navigation3.md` | Back stack, scenes, transitions, and their separation from NavigationBar/Rail presentation |
| Home/list page with search and actions | `MainPage.kt`, `component/SuperSearchBar.kt` | App bar, scroll content, search, section composition, and popup actions |
| Settings page | `SettingsPage.kt` | `SmallTitle` + `Card` grouping, Preference rows, dependent visibility, and content padding |
| Dialog flow | `component/DialogSection.kt` | Overlay/Window choice, trigger rows, visibility, dismissal, actions, and exit cleanup |
| Bottom-sheet flow | `component/BottomSheetSection.kt` | Overlay/Window choice, scrolling content, action layout, and inset handling |
| Dropdown or spinner selection | `component/DropdownSection.kt`, `component/SpinnerSection.kt` | Controlled selection, grouped entries, and row integration |
| Form and basic controls | `component/TextFieldSection.kt`, `SwitchSection.kt`, `CheckboxSection.kt`, `SliderSection.kt` | Controlled state, disabled variants, labels, and validation-relevant states |
| Pull-to-refresh page | `PullToRefreshPage.kt` | Refresh state ownership, scroll integration, and content padding |
| Adaptive, inset, scroll, blur, or haptic helpers | `utils/PageUtils.kt`, then its caller | Call-site intent and platform conditions; copy only the behavior the target app needs |

## Derive a Pattern Before Coding

1. Locate the closest integrated Example page or component section.
2. Identify which parts are Miuix contracts, repeated composition patterns, and Example-specific choices.
3. Compare the integrated use with the isolated `docs/demo/` file for each selected component.
4. Adapt the pattern to the target project's state ownership, navigation, insets, and existing spacing.
5. Verify exact names and parameters in the `v0.9.3` source before producing code.

Do not copy a whole Example file into an application. The Example includes navigation infrastructure, blur helpers, diagnostics, and showcase-only effects that most projects do not need.

## Application Root and Theme

Evidence: `example/shared/src/commonMain/kotlin/App.kt` and `ui/Theme.kt`.

- Apply the ownership decision from [Project setup and theme](setup-and-theme.md): create the root only for a standalone app/example; otherwise integrate below the existing application theme.
- Provide `MiuixTheme` once around the application subtree rather than recreating it in every screen.
- Create `ThemeController` from the application's actual color-mode inputs. Remember it with every input that changes the resulting theme.
- Keep cross-screen theme and UI configuration in stable application state. Use composition locals only when the target architecture already benefits from tree-wide access; they are not required by Miuix.
- Use `MiuixTheme.colorScheme` and `MiuixTheme.textStyles` inside the themed subtree.

## Page Shell and Adaptive Navigation

Evidence: `MainPage.kt`, `SettingsPage.kt`, and `utils/PageUtils.kt`.

- Use `Scaffold` as the page shell when the page has bars, floating controls, snackbars, or Overlay components. Reuse an existing intended host instead of nesting one only for visual structure.
- Consume the `PaddingValues` supplied by `Scaffold`; pass them to `LazyColumn.contentPadding` as the Example does, or apply them to the content root and consume the corresponding window insets as required by the Scaffold contract. Do not let content render underneath bars accidentally.
- Connect a top app bar's scroll behavior to the scrolling content when the page uses collapsing or scroll-aware bars.
- At `v0.9.3`, create `val scrollBehavior = MiuixScrollBehavior()`, pass it to `TopAppBar(scrollBehavior = scrollBehavior)`, and apply `Modifier.nestedScroll(scrollBehavior.nestedScrollConnection)` to the scrolling container. Keep the `LazyListState` used by `LazyColumn(state = ...)` separate from the app-bar behavior state.
- Keep adaptive width, system insets, scroll haptics, blur, and scrollbar helpers conditional. The Example's `PageUtils.kt` is application infrastructure, not public Miuix API.

### NavigationBar ↔ NavigationRail switching

The `v0.9.3` Example computes its own window policy from `LocalWindowInfo`: it uses the wide/split layout at `width >= 840.dp`, or at `width >= 600.dp` when `height / width < 1.2`; it expands the rail at `width >= 1200.dp`. These are Example application breakpoints, not Miuix component requirements.

- Reuse the target project's existing adaptive system. Android-only apps may already use a Window Size Class; multiplatform apps may use `LocalWindowInfo` or another shared policy. Keep the decision in one named function rather than scattering width checks through components.
- Remember or hoist the selected destination/pager/back-stack before branching. Render `NavigationBar` and `NavigationRail` from that same state, so a resize changes chrome without resetting selection or creating two sources of truth.
- Use compact/wide branches for materially different shells. If only the navigation component changes, keep page content and business state outside the branch.
- Treat `600/840/1200.dp` as a source-derived starting point only when the product has no existing policy, then validate resizable windows, orientation changes, and foldable/tablet layouts.

## Navigation 3 Routing

Use Navigation 3 for route/back-stack ownership, and use `NavigationBar`/`NavigationRail` for top-level navigation chrome. At `v0.9.3`, the minimal route flow is `NavKey` → mutable `backStack` → `entryProvider` → `rememberDecoratedNavEntries` → `NavDisplay`:

```kotlin
sealed interface Route : NavKey {
    data object Home : Route
    data class Detail(val id: String) : Route
}

val backStack = remember { mutableStateListOf<NavKey>(Route.Home) }
val entryProvider = remember(backStack) {
    entryProvider<NavKey> {
        entry(Route.Home) { HomePage() }
        entry<Route.Detail> { DetailPage(it.id) }
    }
}
val entries = rememberDecoratedNavEntries(backStack = backStack, entryProvider = entryProvider)
NavDisplay(entries = entries, onBack = { if (backStack.size > 1) backStack.removeLast() })
```

For a bottom/rail navigation shell, derive each item's `selected` and `onClick` from the same top-level page or pager state used by the content. Do not derive tab selection from a `NavDisplay` entry or create separate compact and wide selection states. Use `rememberNavBackStack` plus saved-state configuration when the target app needs restoration beyond the small in-memory pattern above. Read `docs/guide/navigation3.md` and the Example `AppContent.kt` before adding transitions or nested routes.

## Settings and Grouped Content

Evidence: `SettingsPage.kt` and the files under `example/shared/src/commonMain/kotlin/component/`.

- Build a settings page from scrollable sections. Use the [Preference family guide](preferences-and-menus.md#choose-the-family) to distinguish a complete setting row from a standalone control or menu.
- Use `SmallTitle` to label a section and `Card` to group related Preference components.
- Put related `SwitchPreference`, `CheckboxPreference`, `ArrowPreference`, dropdown, and slider rows in the same Card rather than styling every row as an unrelated surface.
- Use `summary` for consequences or context, not to repeat the title.
- Show dependent settings conditionally, as the Example does with `AnimatedVisibility`, while keeping their state owned by the screen or application model.
- Hide a dependent setting when it is irrelevant or unsupported; leave it visible but disabled when discoverability matters and the summary can explain the prerequisite. See [Preference enabled and dependent states](preferences-and-menus.md#enabled-and-dependent-states).
- Treat the Example's repeated 12 dp group margins as a reference value, not a global Miuix constant. Preserve the target project's established spacing unless the user asks to change it, and use [Design language and Defaults](design-language.md) for semantic styling decisions.

Reference shape, adapted from `SettingsPage.kt`:

```kotlin
@Composable
fun SettingsScreen(
    enabled: Boolean,
    onEnabledChange: (Boolean) -> Unit,
) {
    Scaffold(
        topBar = { TopAppBar(title = "Settings") },
    ) { innerPadding ->
        LazyColumn(contentPadding = innerPadding) {
            item {
                SmallTitle(text = "General")
                Card(
                    modifier = Modifier
                        .padding(horizontal = 12.dp)
                        .padding(bottom = 12.dp),
                ) {
                    SwitchPreference(
                        title = "Feature",
                        summary = "Explain what enabling it changes",
                        checked = enabled,
                        onCheckedChange = onEnabledChange,
                    )
                }
            }
        }
    }
}
```

Use this as a structural pattern. Confirm imports and exact component APIs against the target project and pinned source.

## State and Component Variants

Evidence: `docs/demo/` and the Example component sections.

- Hoist persistent or business state to the owning screen/model. Use `remember` for transient presentation state such as whether a local popup is open.
- Pass the current value and callback to controlled components (`checked` + `onCheckedChange`, `selectedIndex` + callback, `value` + `onValueChange`).
- Cover relevant states demonstrated by the component evidence: normal, selected/checked, summary, disabled, expanded, and dismissal. Do not add showcase states that the product does not need.
- Prefer the component's default colors, margins, shapes, and haptics. Reach for `ComponentDefaults` only when customization has an explicit reason.

## Overlay and Window Components

Evidence: `component/DialogSection.kt`, `component/BottomSheetSection.kt`, and their matching `docs/demo/` files.

- Choose the lifecycle/host family with [Overlay and Window Components](overlays-and-windows.md#choose-the-host-first).
- Use Overlay components for page-scoped UI and place them under the intended `Scaffold` popup host.
- Use Window components when the popup must be independent of that page's Scaffold.
- Model visibility explicitly with `show` and close it through `onDismissRequest`.
- Always make `onDismissRequest` set the controlled `show`/active-overlay state to false or null. System back and outside-click dismissal request this callback; the callback does not own the Boolean for you.
- Use `onDismissFinished` only when cleanup must wait for the exit animation, such as resetting a pressed/hold-down state or discarding a draft after the popup is no longer visible. It is not called when a hide animation is cancelled by showing the component again.
- Keep dialog actions balanced and make the primary action visually primary with the provided button defaults when the evidence supports it.

For multiple popup entry points, prefer one page-scoped state such as `ActiveOverlay?` and render each popup from an equality check. The current Overlay host assigns internal z-indices, but that ordering is not a public workflow contract; do not depend on arbitrary simultaneous `show = true` values. An intentional parent → child popup flow should dismiss child first and must be tested for back, focus, scrim, and accessibility behavior.

Keep the pattern small: one enum chooses the active popup, while `onDismissFinished` handles only cleanup that must wait for animation completion.

```kotlin
private enum class ActiveOverlay { EditName, DeleteConfirm }

var activeOverlay by remember { mutableStateOf<ActiveOverlay?>(null) }
var nameDraft by remember { mutableStateOf(savedName) }

OverlayDialog(
    show = activeOverlay == ActiveOverlay.EditName,
    onDismissRequest = { activeOverlay = null },
    onDismissFinished = { nameDraft = savedName },
) { /* edit nameDraft; commit only from the confirm action */ }
```

Place this flow below one intended `Scaffold`. Render the second dialog from `activeOverlay == ActiveOverlay.DeleteConfirm` and give it the same dismissal contract. If one popup must open another, close the current one first and activate the next only after its dismissal finishes; add queued state only for that real product flow.

### Form popup lifecycle

Keep saved/business data outside the popup and follow this compact lifecycle:

1. On open, copy the saved value into a local draft.
2. Edit and validate the draft inside the popup; validation failure keeps it open and preserves input.
3. Confirm commits the draft, then requests dismissal.
4. Cancel, outside click, or system back requests dismissal without committing.
5. Reset or discard the draft in `onDismissFinished` only when cleanup must wait for the exit animation.

All delivered code must satisfy the single authoritative [Code Delivery Contract](../SKILL.md#code-delivery-contract).
