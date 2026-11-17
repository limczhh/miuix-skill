# Integrated Usage Patterns

This is the integrated pattern catalogue for the design specification. At the current source-audit snapshot (`main` at `d8efe1872c08c863a8b33e7044dd2a8b43038032`), the upstream best-practices guide points to the Example application as a real-world demonstration of how Miuix components and design principles are applied. Use the matching pattern when the task needs integrated hierarchy, state flow, adaptive composition, or the optional Glass/backdrop composition. Example code is never the API authority; the profile metrics in [Design language and Defaults](design-language.md) are the default design rules.

## Contents

- [Evidence Levels](#evidence-levels)
- [Scenario to Integrated Example Map](#scenario-to-integrated-example-map)
- [Derive a Pattern Before Coding](#derive-a-pattern-before-coding)
- [Application Root and Theme](#application-root-and-theme)
- [Page Shell and Adaptive Navigation](#page-shell-and-adaptive-navigation)
- [`miuix-nav` Routing](#miuix-nav-routing)
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
| `miuix-nav` routing and transitions | `App.kt`, `AppState.kt`, `AppContent.kt`, `navigation/`, then `docs/guide/miuix-nav.md` | Serializable back stack, entry DSL, transitions, gestures, and their separation from NavigationBar/Rail presentation |
| Home/list page with search and actions | `MainPage.kt`, `component/SuperSearchBar.kt` | App bar, scroll content, search, section composition, and popup actions |
| Settings page | `SettingsPage.kt` | `SmallTitle` + `Card` grouping, Preference rows, dependent visibility, and content padding |
| Glass page and backdrop-based shell | `GlassPage.kt`, `GlassSearchOverlay.kt`, `component/GlassBottomSheetDemo.kt`, `utils/PageUtils.kt` | Backdrop recording boundary, Glass bars/controls, overlay sampling, and effect fallback; treat Glass as an optional family |
| Dialog flow | `component/DialogSection.kt` | Overlay/Window choice, trigger rows, visibility, dismissal, actions, and exit cleanup |
| Bottom-sheet flow | `component/BottomSheetSection.kt` | Overlay/Window choice, scrolling content, action layout, and inset handling |
| Dropdown or spinner selection | `component/DropdownSection.kt`, `component/SpinnerSection.kt` | Controlled selection, grouped entries, and row integration |
| Form and basic controls | `component/TextFieldSection.kt`, `SwitchSection.kt`, `CheckboxSection.kt`, `SliderSection.kt` | Controlled state, disabled variants, labels, and validation-relevant states |
| Pull-to-refresh page | `PullToRefreshPage.kt` | Refresh state ownership, scroll integration, and content padding |
| Adaptive, inset, scroll, blur, or haptic helpers | `utils/PageUtils.kt`, then its caller | Call-site intent and platform conditions; copy only the behavior the target app needs |

## Derive a Pattern Before Coding

1. Confirm or write the page map before opening an integrated Example page. Open [Page structure and ownership](page-structure.md) only if ownership or the page boundary is still unresolved.
2. Locate the closest Example page or component section for the content relationship, not for a mandatory page type or component recipe.
3. Separate Miuix contracts, repeated composition patterns, Example-specific choices, and target-project conventions.
4. Compare only the selected components with their isolated `docs/demo/` files.
5. Apply the profile metrics by default, then adapt only the state ownership, navigation, insets, and any established Brownfield convention that actually differs. Verify uncertain names and parameters in the pinned source.

Do not copy a whole Example file into an application. The Example includes navigation infrastructure, blur helpers, diagnostics, and showcase-only effects that most projects do not need.

## Application Root and Theme

Evidence: `example/shared/src/commonMain/kotlin/App.kt` and `ui/Theme.kt`.

- Apply the ownership decision from the `miuix-components` Skill's Project Setup and Theme reference: create the root only for a standalone app/example; otherwise integrate below the existing application theme.
- Provide `MiuixTheme` once around the application subtree rather than recreating it in every screen.
- Create `ThemeController` from the application's actual color-mode inputs. Remember it with every input that changes the resulting theme.
- Keep cross-screen theme and UI configuration in stable application state. Use composition locals only when the target architecture already benefits from tree-wide access; they are not required by Miuix.
- Use `MiuixTheme.colorScheme` and `MiuixTheme.textStyles` inside the themed subtree.

## Page Shell and Adaptive Navigation

Evidence: `MainPage.kt`, `SettingsPage.kt`, and `utils/PageUtils.kt`.

- Use the [page structure rules](page-structure.md) to decide whether the page owns a shell. The Example separates an outer shell `Scaffold` for persistent navigation/global layers from a page `Scaffold` for a page app bar; this nesting is valid because the ownership differs. Use `Scaffold` when the relevant boundary has bars, floating controls, snackbars, or Overlay components that belong to it, and reuse an existing intended host instead of nesting one only for visual structure.
- Consume the `PaddingValues` supplied by `Scaffold`; pass them to `LazyColumn.contentPadding` as the Example does, or apply them to the content root and consume the corresponding window insets as required by the Scaffold contract. Do not let content render underneath bars accidentally.
- Connect a top app bar's scroll behavior to the scrolling content when the page uses collapsing or scroll-aware bars.
- At the pinned Example snapshot, create `val scrollBehavior = MiuixScrollBehavior()`, pass it to `TopAppBar(scrollBehavior = scrollBehavior)`, and apply `Modifier.nestedScroll(scrollBehavior.nestedScrollConnection)` to the scrolling container. Keep the `LazyListState` used by `LazyColumn(state = ...)` separate from the app-bar behavior state. When a `SmallTopAppBar` shares a scroll behavior with a collapsible bar, verify the pinned-state behavior in source rather than adding an external offset workaround.
- If the page owns a `FloatingToolbar`, put it in the existing `Scaffold(floatingToolbar = ..., floatingToolbarPosition = ...)` slot; the current layout keeps a bottom snackbar above the toolbar.
- Keep adaptive width, system insets, scroll haptics, blur, and scrollbar helpers conditional. The Example's `PageUtils.kt` is application infrastructure, not public Miuix API.

### NavigationBar ↔ NavigationRail switching

- Reuse the target project's existing adaptive system. Android-only apps may already use a Window Size Class; multiplatform apps may use `LocalWindowInfo` or another shared policy. Keep the decision in one named function rather than scattering width checks through components.
- Remember or hoist the selected destination/pager/back-stack before branching. Render `NavigationBar` and `NavigationRail` from that same state, so a resize changes chrome without resetting selection or creating two sources of truth.
- Use compact/wide branches for materially different shells. If only the navigation component changes, keep page content and business state outside the branch.
- Keep any fallback breakpoints in one named policy and validate resizable windows, orientation changes, and foldable/tablet layouts.

## `miuix-nav` Routing

Use `miuix-nav` for route/back-stack ownership, and use `NavigationBar`/`NavigationRail` for top-level navigation chrome. Use the complete miuix-nav reference in the `miuix-components` Skill for the exact route key, back-stack, entry, transition, save/restore, and v1 limitation contracts.

For a bottom/rail navigation shell, derive each item's `selected` and `onClick` from the same top-level page or pager state used by the content. Do not derive tab selection from a `NavDisplay` entry or create separate compact and wide selection states. Do not import the removed `androidx.navigation3` scene flow into current code.

## Settings and Grouped Content

Evidence: `SettingsPage.kt` and the files under `example/shared/src/commonMain/kotlin/component/`.

This is a content-composition example, not a different page skeleton. The same page-host, content-owner, inset, and state rules apply to pages whose main content is not a settings group. The current Example also contains Glass pages; use them only when the target explicitly adopts the optional Glass family.

- When settings content is long, use a scrollable main content flow. Use the Preference and Dropdown Menu reference in the `miuix-components` Skill to distinguish a complete setting row from a standalone control or menu. The current Example may use `GlassDropdownPreference` in Glass-oriented sections; do not replace ordinary preferences with it unless the target also uses the Glass/backdrop contract.
- When a group needs a section label and shared containment, use `SmallTitle` and `Card`; choose another content composition when those semantics do not apply.
- Put related `SwitchPreference`, `CheckboxPreference`, `ArrowPreference`, dropdown, and slider rows in the same Card when they are part of one meaningful setting group rather than styling every row as an unrelated surface.
- Use `summary` for consequences or context, not to repeat the title.
- Show dependent settings conditionally, as the Example does with `AnimatedVisibility`, while keeping their state owned by the screen or application model.
- Hide a dependent setting when it is irrelevant or unsupported; leave it visible but disabled when discoverability matters and the summary can explain the prerequisite. Use the `miuix-components` Preference reference for the exact row capabilities.
- Use the profile's grouped-content rule: `SmallTitle` followed by a `Card` with `12.dp` horizontal outer inset and `12.dp` bottom gap. This is a design-specification rule, not a Miuix library constant. In Brownfield work, preserve a clearly established target convention when the surrounding page system consistently uses another value.

Use the integrated settings files only to inspect grouping, dependent visibility, state flow, and content-padding decisions. Do not copy their page shell or row composition without checking that the target content has the same semantics. Confirm all selected component APIs against the target project and pinned source.

## State and Component Variants

Evidence: `docs/demo/` and the Example component sections.

- Hoist persistent or business state to the owning screen/model. Use `remember` for transient presentation state such as whether a local popup is open.
- Pass the current value and callback to controlled components (`checked` + `onCheckedChange`, `selectedIndex` + callback, `value` + `onValueChange`).
- Cover relevant states demonstrated by the component evidence: normal, selected/checked, summary, disabled, expanded, and dismissal. Do not add showcase states that the product does not need.
- Prefer the component's default colors, margins, shapes, and haptics. Reach for `ComponentDefaults` only when customization has an explicit reason.

## Overlay and Window Components

Evidence: `component/DialogSection.kt`, `component/BottomSheetSection.kt`, and their matching `docs/demo/` files.

- Choose the lifecycle/host family with the Overlay and Window reference in the `miuix-components` Skill.
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
