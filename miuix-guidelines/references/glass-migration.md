# Glass Migration and Coexistence

Use when replacing existing ordinary components with the optional Glass family, adopting only a Glass surface, or supporting both presentations. This guide targets **`0.9.5-beta01`**, not a future stable release. Confirm the [beta01 dependency and platform setup](../../miuix-components/references/setup-and-theme.md) first. Ordinary and Glass components below both use this one library version; this guide does not maintain older-release APIs. Mandatory visual/ownership requirements remain in [page-shell rules](rules/page-shell.md#blur-and-glass) and [popup rules](rules/settings-and-overlays.md#popup-family-and-lifetime); this file supplies migration decisions and examples.

Contents: [scope](#choose-the-migration-boundary) · [replacement map](#replacement-map) · [sampling](#record-the-right-scene) · [bars](#replace-a-top-bar) · [navigation](#keep-ordinary-and-glass-navigation-together) · [floating actions and stroke](#migrate-floating-actions-and-their-bloom-stroke) · [overlays](#adapt-an-existing-overlay-surface) · [menus](#migrate-menus-and-setting-rows) · [coexistence](#own-coexistence-policy-once) · [acceptance](#acceptance).

## Choose the migration boundary

Start from [Brownfield evolution](brownfield-evolution.md): locate the real caller, wrapper, route state, list/pager state, inset handoff, popup host and dismissal chain. Mark what is preserved, replaced and added before changing presentation.

| Intent | Smallest useful scope |
|---|---|
| Keep the current ordinary appearance | Use beta01 ordinary components; adopting Glass is optional |
| Give one dialog/sheet a Glass surface | Keep the existing Overlay host and content; use its new typed `surface` slot |
| Change an entire chrome family | Use Glass app bar/navigation/tab/action components, preserving route and scroll owners |
| Offer ordinary and Glass styles | Put one presentation policy above render branches; share state, callbacks and content |
| Use Glass only where runtime effects are available | Decide whether unsupported targets keep Glass geometry with opaque fill or render the ordinary family; both are valid product choices |

Add only used dependencies. `miuix-glass` brings UI/material/blur transitively; `miuix-glass-icons` is optional and does not make icons in other sets obsolete. An Android app below API 33 needs the [documented blur manifest decision](../../miuix-components/references/setup-and-theme.md#quick-start) even if it renders an opaque fallback at runtime.

## Replacement map

These are migration candidates, not aliases or mandatory replacements. Verify exact signatures in [Glass components](../../miuix-components/references/glass.md) and the target source before copying calls.

| Existing component or treatment | Glass candidate | Preserve / adapt |
|---|---|---|
| `TopAppBar` plus ordinary blurred background | `GlassTopAppBar` | Same page scroll behavior, back/actions, width policy and header slot. Replace the blur wrapper so the new bar is not double-blurred. |
| Wide fixed header / custom `SmallTopAppBar` composition | `GlassTopAppBar(expandable = false)` when its layout fits | Fixed-title intent and content inset. Keep a special hero header if its structure matters. |
| `NavigationBar` / `FloatingNavigationBar` | `GlassNavigationBar` | One destination model, selection and route callbacks; convert item models and revise component-owned bottom spacing. |
| Ordinary `NavigationRail` | `GlassNavigationRail` | Same destination owner, but a **different overlay/morph layout** and separate Glass rail state; revise content clearance and title overlap. |
| `TabRow` / `TabRowWithContour` | `GlassTabRow` / `GlassSegmentedTabRow` | Same pager owner; callbacks become `selectedIndex` / `onSelect`. Bind `selectedIndex` to `pagerState.targetPage` to prevent indicator rebound on touch release. Segmented variant self-downgrades to opaque resting track and bloom stroke when blur is disabled (pass an unrecorded dummy `Backdrop` or inherit top-bar context; no need to revert to ordinary tabs). |
| `IconButton` | `GlassIconButton` | Same action/description. Inside `GlassTopAppBar`, inherit its material by default; outside it, supply the intended backdrop. Beta01 has no `enabled` parameter: retain an ordinary control or a verified semantic adapter when disabling is required; a no-op callback alone does not preserve disabled semantics. |
| `FloatingActionButton` / `FloatingToolbar` | `GlassFloatingActionButton` / `GlassFloatingToolbar` | Keep Scaffold slots and action ownership; adopt the built-in Glass material, bloom rim and separate shadow. See [floating-action migration](#migrate-floating-actions-and-their-bloom-stroke) before retaining an application border or blur wrapper. |
| `OverlayDialog` | Keep it with `glassDialogSurface`, or use `GlassDialog` | Existing show/draft/confirm/finished state and dialog geometry; Glass defaults include their own material/scrim. |
| `OverlayBottomSheet` | Keep it with `glassBottomSheetSurface`, or use `GlassBottomSheet` | Existing drag/list state, nesting and actions. Surface helper handles top corners and overscroll extension. |
| `WindowDialog` / `WindowBottomSheet` | Retain Window, or explicitly migrate host to Overlay/Glass | Glass wrappers are Scaffold-hosted; they are **not** Window replacements and Window APIs do not gain the typed surface slots. |
| Icon dropdown / cascading menu | Surface-taking ordinary cascading overload, or `GlassIconDropdownMenu` | Ordinary host can remain; full Glass menu instead needs root Box, anchor recording and controlled visibility. |
| Dropdown preference row | `GlassDropdownPreference` when its selection model fits | Caller-owned value; component-owned expansion. Do not map richer spinner items to string choices blindly. |
| Card/custom panel | Ordinary Card body plus selected custom `glassPanel` surface | Card click/measurement semantics and opaque fallback; no universal `GlassCard` or glass preference/control for every ordinary component. |
| Extended icons | Optional `MiuixIcons.Glass` extensions | Verify each semantic symbol; no mechanical one-to-one mapping or compulsory icon replacement. |

Ordinary text, switches, checkboxes, fields, progress and section hierarchy can remain inside a Glass shell. A Glass upgrade does not require translucent content groups everywhere.

## Record the right scene

Create the recording at the owner whose scene the surface must sample. Include an opaque base *inside* the recorded subtree. Keep the Glass surface and its scrim outside that subtree.

```text
shell or page root
├── opaque recorded page content → backdrop
├── Glass top/bottom/rail layer → samples backdrop
└── popup host or root-box popup → samples scene, excluded from it
```

If a dialog must sample the page **including its bars**, record that page's Scaffold as content under an outer popup-hosting Scaffold. Route the dialog to the outer host. Do not record that outer host or its scrim. If sampling content alone is intentional, the simpler content-only recording is sufficient. The number of hosts follows the scene/lifetime boundary; it is not a mandatory architecture copied from Example.

## Replace a top bar

The following is a placement shape under the existing theme. Ordinary Compose imports and application strings/actions are caller-supplied; the Miuix API calls are catalog-verified, not target-compiled.

```kotlin
import top.yukonga.miuix.kmp.basic.MiuixScrollBehavior
import top.yukonga.miuix.kmp.basic.Scaffold
import top.yukonga.miuix.kmp.blur.layerBackdrop
import top.yukonga.miuix.kmp.blur.rememberLayerBackdrop
import top.yukonga.miuix.kmp.glass.GlassIconButton
import top.yukonga.miuix.kmp.glass.GlassTopAppBar
import top.yukonga.miuix.kmp.material.isRuntimeShaderSupported
import top.yukonga.miuix.kmp.utils.overScrollVertical

val recorded = rememberLayerBackdrop()
val sampling = if (effectsEnabled && isRuntimeShaderSupported()) recorded else null
val listState = rememberLazyListState()
val behavior = MiuixScrollBehavior()
val onRebound: (Float) -> Unit = remember(behavior.state) {
    { offset -> behavior.state.overscrollOffset = offset }
}

Scaffold(
    topBar = {
        GlassTopAppBar(
            title = title,
            isContentScrolled = listState.canScrollBackward,
            expandable = !isWideWindow,
            backdrop = sampling,
            scrollBehavior = behavior,
            navigationIcon = {
                GlassIconButton(onClick = onBack) { backIconContent() }
            },
        )
    },
) { padding ->
    LazyColumn(
        state = listState,
        modifier = Modifier
            .fillMaxSize()
            .overScrollVertical(onOffsetChanged = onRebound)
            .nestedScroll(behavior.nestedScrollConnection)
            .then(if (sampling != null) Modifier.layerBackdrop(recorded) else Modifier)
            .background(MiuixTheme.colorScheme.surface)
            .consumeWindowInsets(padding),
        contentPadding = padding,
        overscrollEffect = null,
    ) {
        // Existing content and stable item keys.
    }
}
```

Here `isWideWindow` comes from the shared application width policy. For a real shell, retain its effective safe padding and bottom breathing room from the [inset contract](rules/page-shell.md#insets-and-content-padding). If a shared scroll helper already installs overscroll/haptics/bar linkage, update that helper instead of appending this second chain. A refresh wrapper must not also install the same bar connection.

Use explicit `isContentScrolled` when real list overlap/programmatic movement must drive floating state. The visual displacement bridge supplies elastic motion; it is not a replacement for actual list position. `progressiveBlur` is an optional band effect, independent of title blur. Glass owns its button material and band; remove the old ordinary bar background where the Glass bar replaces it. `expandable = false` avoids stacking a hidden large Glass title behind another visible small-title component.

### Child controls, overscroll motion, and Pager state linkage

When placing `GlassTabRow`, `GlassSegmentedTabRow`, or custom pill actions inside `GlassTopAppBar(bottomContent = ...)` or as a pinned header strip:

1. **Overscroll rebound linkage**: Mirror `GlassTopAppBar`'s internal material float by taking the maximum of scrolled state and overscroll ramp:
   ```kotlin
   val ramp = GlassTopAppBarDefaults.collapseRamp(scrollBehavior)
   val scrolledProgress by animateFloatAsState(
       targetValue = if (listState.canScrollBackward) 1f else 0f,
       animationSpec = GlassMotion.topBarButtonFloat(),
   )
   val surfaceAlpha by remember(scrolledProgress, scrollBehavior, ramp) {
       derivedStateOf {
           val elastic = if (scrollBehavior?.state?.overscrollOffset != 0f) ramp.value else 0f
           maxOf(scrolledProgress, elastic)
       }
   }
   ```
   This ensures child glass controls spring and reveal their stroke and material dynamically during vertical pull-down overscroll, rather than remaining flat or snapping abruptly.

2. **Pager state binding and indicator stability**: When binding `GlassSegmentedTabRow` to `HorizontalPager`, always bind `selectedIndex = pagerState.targetPage` rather than `pagerState.currentPage`. Because `GlassSegmentedTabRow` increments its internal `settleGeneration` counter to align the sliding indicator immediately upon pointer release, binding `currentPage` (which updates only after the pager finishes scrolling or crosses the halfway threshold) pulls the sliding indicator backward toward the previous page before re-animating forward.

## Keep ordinary and Glass navigation together

Keep the destination data, selected route, list/pager state and callback outside presentation branches. Adapt the same destination model into the family-specific item type:

```kotlin
val glassItems = destinations.map { destination ->
    GlassNavigationItem(
        icon = destination.icon,
        label = destination.label,
        contentDescription = destination.label,
    )
}

// Inside the one owning Scaffold.bottomBar:
if (useGlassChrome) {
    GlassNavigationBar(
        items = glassItems,
        selectedIndex = selectedIndex,
        onSelect = onDestinationSelected,
        backdrop = sampling,
    )
} else {
    ordinaryNavigation(selectedIndex, onDestinationSelected)
}
```

`ordinaryNavigation` is the application's existing ordinary/floating bar wrapper. Glass uses `GlassNavigationItem` from `top.yukonga.miuix.kmp.glass`, not the ordinary item model. Preserve badges explicitly when adapting models. Render one active navigation surface; do not duplicate route mutations or hide an interactive second bar with alpha.

For wide windows, `GlassNavigationRail` is an overlay that fills its given space and morphs into a top capsule. Do not put it in the old rail's width-constrained Row and assume equivalent measurement. Reuse one remembered `GlassNavigationRailState`; derive page clearance and title visibility from its layout/draw `progress`. Keep rail overlays outside recording and coordinate top controls when the capsule overlaps them. `secondaryItems` appear while expanded and use indices after the primary `items` (`items.size + secondaryIndex`); ensure the selected secondary destination stays accessible when the rail collapses or the layout becomes compact. Use the ordinary rail when the requested migration does not include this layout change.

## Migrate floating actions and their bloom stroke

The ordinary `FloatingActionButton` supplies a colored surface and elevation shadow; it does not supply the Glass bloom rim. `GlassFloatingActionButton` includes that rim by default through `stroke`, independently of its drop shadow. A color-only or translucent ordinary FAB is not the same treatment. The exact defaults for the FAB and toolbar live in the [floating-action contract](../../miuix-components/references/contracts/glass.md#glassfloatingactionbutton--glassfloatingtoolbar).

Replace the control in its existing `Scaffold.floatingActionButton` slot, preserve `onClick` and icon semantics, and pass the scene backdrop already owned by the shell/page:

```kotlin
import top.yukonga.miuix.kmp.glass.GlassFloatingActionButton

// Inside the existing Scaffold.floatingActionButton slot:
GlassFloatingActionButton(
    onClick = onAction,
    backdrop = sampling,
    // Omitting stroke preserves the built-in theme-adapted bloom rim.
) {
    actionIconContent()
}
```

Remove a superseded application blur wrapper or duplicate `border`/`edgeStroke` around this control; do not draw a second rim just to obtain the default effect. Set `stroke = null` only to deliberately remove the rim, and `shadow = null` only to remove the drop shadow. Neither disables background sampling. Use the matching `GlassFloatingToolbar` in `Scaffold.floatingToolbar` for an action group; it also includes its own default bloom stroke.

With `backdrop = null`, the Glass FAB uses its opaque fill and still attempts the rim when runtime shaders are supported. If runtime shaders are unavailable, the rim drawing is skipped; a readable opaque fallback does not guarantee the same optical highlight. Check effects-off and unsupported-runtime states separately. For a rim-only treatment that deliberately retains an ordinary component, use the separate [material edge-stroke modifier](../../miuix-components/references/styling-icons-and-effects.md#glass-and-material-boundary) with a matching shape; do not describe it as a full Glass component migration.

## Adapt an existing overlay surface

For a dialog already using `OverlayDialog`, the smallest host-preserving migration is:

```kotlin
import top.yukonga.miuix.kmp.glass.GlassDialogDefaults
import top.yukonga.miuix.kmp.glass.glassDialogSurface
import top.yukonga.miuix.kmp.overlay.OverlayDialog

OverlayDialog(
    show = showDialog,
    onDismissRequest = closeWithoutSaving,
    onDismissFinished = finishDialogSession,
    dimColor = GlassDialogDefaults.ScrimColor,
    surface = if (useGlassSurface) glassDialogSurface(backdrop = sampling) else null,
) {
    editorContent()
}
```

The callbacks and content are application-owned. If the ordinary branch needs a different scrim, choose its color with the same presentation policy. `surface = null` preserves the ordinary surface. A Glass helper with `backdrop = null` retains Glass geometry with its opaque fill; these are two different coexistence choices.

For a sheet, use `OverlayBottomSheet(surface = glassBottomSheetSurface(sampling))`; do not pass `glassSurface` to the typed sheet/dialog slot. A dialog surface receives radius, animated scrim and scale; a sheet surface receives radius, animated scrim and overscroll-extension status. The helpers compensate host animation and sampled scrim correctly. Keep `show = false` composed until `onDismissFinished`; preserve draft initialization on each new opening and confirm/cancel behavior.

Full `GlassDialog` / `GlassBottomSheet` wrappers select the matching Overlay host and Glass defaults for you. They are useful when adopting the whole appearance contract, but do not accept every parameter of the old component. Do not replace a Window component by name without auditing back dispatch, focus/IME, clipping, render-in-root behavior and feedback visibility.

## Migrate menus and setting rows

For existing cascading popups, first consider the added `surface` overload with `glassSurface(backdrop, visuals)`: it preserves that popup family's anchor and Overlay/Window hosting. A full `GlassIconDropdownMenu` instead takes a remembered `GlassPopupAnchor`, `anchorContent`, `entries` and caller-controlled `show`; put the popup in a suitable root `BoxScope` beside the recorded page. A popup nested under the sampling layer can sample itself or be clipped under other chrome.

Keep the anchor alive through exit. Register outer control bounds with `glassPopupAnchor`, and the copied glyph/content with `glassPopupAnchorContent` where the transform family requires it. Reuse existing `DropdownEntry` / `DropdownItem` actions, enabled state and children. Do not attach both the old menu and new Glass menu to the same click.

`GlassDropdownPreference` owns expansion, while the caller owns `selectedIndex` and persistence. It takes string items, not every ordinary spinner model. Keep richer controls, secondary actions, disabled reasons and saved values in their existing form unless an explicit semantic migration is justified.

## Own coexistence policy once

Distinguish **presentation family** from **effect capability**:

- Ordinary/Glass selection belongs to the shell/shared presentation owner. Add a saved user option only when the product asks for one; otherwise a scoped fixed policy is enough.
- Effect permission/support decides whether the selected family samples a backdrop. Nullable-backdrop components can retain their geometry with opaque fill. A low-level `glassPanel` defaults only to clipping when fallback is omitted, so custom panels must supply their readable fallback.
- Distinguish low-level `Modifier.glass` from composed `GlassSegmentedTabRow`: `Modifier.glass` requires a non-null Backdrop and active runtime shader. `GlassSegmentedTabRow` requires a non-null `Backdrop` in its signature, but internally self-downgrades to its solid `restingTrackColor`, indicator, and bloom stroke when `runtimeShaderSupported` is false or backdrop is unrecorded. When disabling blur effects, pass a dummy `rememberLayerBackdrop()` (or inherit `LocalGlassTopAppBarContext`) to preserve the pill-segmented presentation without reverting to legacy underline tabs.
- Keep one canonical selected route, pager, scroll behavior, form model, draft and loading owner. Keep family-specific visual state only where required, for example Glass rail expansion.
- For an open popup, latch its chosen family/host for the current session through dismissal completion. A style change must not recreate the editor, discard its draft, or move it between Window and Overlay mid-animation. Apply the new policy at the next session, or explicitly close then switch after completion.
- Remove superseded blur/press/layout copies once all callers have moved; keep the ordinary implementation only when it still serves the chosen fallback/coexistence branch. Avoid maintaining two entire page trees.

## Acceptance

Compile the affected target and dependent UI module. Then check the chosen migration boundary: both presentations, effect on/off, supported/unsupported runtime, forced light/dark/dynamic themes, compact/wide resizing and RTL, list/pager position, rapid actions, badge/secondary selection, popup cancellation and reopen, exiting animations and focus/IME.

Inspect the rendered backdrop to confirm an opaque base, no self-sampling/scrim duplication, and correct moving coordinates. Compare frame/device behavior for added recording/progressive effects before claiming performance parity or improvement. A sources.jar match or signature check proves neither visual quality nor runtime migration success.
