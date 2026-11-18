# Component Selection

Use this catalog when the available public Miuix component family or capability is unclear. For a general library tour or module/dependency question, start with the [Library overview](library-overview.md); this catalog begins after the semantic role is understood. It is a library lookup table, not a page workflow. If the component and call are already known, skip this file and use the exact API or host reference directly.

Read only the matching module table or boundary note; do not scan the entire catalog for a single component.

Contents: [component table](#component-reference-table) · [public API boundaries](#public-api-boundaries) · [validation notes](#textfield-validation)

## Match a semantic role to a public component

This catalog helps identify a public component for an already-understood semantic role. It does not define a universal page template or decide the page's ownership model; those are handled by the `miuix-guidelines` Skill. When the role is clear, choose the least specialized public component that expresses it:

- Use a host component only when the page owns host behavior.
- Choose one primary content owner and let its children follow the content's semantics.
- Use Preference components for complete setting rows, not merely because a page happens to be a settings-like screen.
- Use `Card`, `SmallTitle`, custom layouts, or plain Compose primitives when their containment or content relationship is justified.
- Keep the component choice independent from route and navigation state ownership.

## Source-backed cues for vague requests

Use these cues to reach the right family when the user describes behavior instead of a symbol. They narrow the search; they do not authorize an import or an exact call without declaration verification.

| User describes | Inspect first | Boundary confirmed by the pinned source |
|---|---|---|
| Search input that expands to show results | `SearchBar` and `InputField` in `miuix-ui/basic` | `SearchBar` renders the supplied expanded content and `InputField` exposes query/editing callbacks; a simple text field need not use the expanded container |
| Several destinations at the bottom | `NavigationBar` / `FloatingNavigationBar` in `miuix-ui/basic` | Both navigation-bar families document 2–5 items; shell and selected-destination ownership remains a page-composition decision |
| A setting row that opens a value choice | `miuix-preference` preference/menu families | Complete row semantics, plain dropdown values, richer spinner items, and anchored menus are different roles; do not choose by the word “settings” alone |
| A popup, dialog, or sheet | Overlay/Window families | Page lifetime and Scaffold ownership decide the host family; verify `renderInRootScaffold` for nested page hosts |
| A title that collapses while content scrolls | `TopAppBar` and `MiuixScrollBehavior` | The behavior exposes a nested-scroll connection that must be attached to the same primary scroll owner; this is composition evidence, not a free-standing visual parameter |
| Glass/frosted surface, bar, or popup | `miuix-glass` family | A Glass surface samples a `Backdrop`; the recorded content must exclude the surface. `GlassDialog`/`GlassBottomSheet`/`GlassDropdownPreference` use the `Scaffold` popup host, while the `BoxScope` popup family needs a shared root `BoxScope` with its anchor |
| Desktop custom title bar or native window styling | `miuix-window` | This is a `desktopMain`-only window host, separate from common `miuix-ui` `WindowDialog` and popup components |

## Component Reference Table

When the user asks about a component, first match it case-insensitively against this table, then read files at the depth appropriate to the user's intent.

### Compose primitives used with Miuix

`LazyColumn`, `LazyRow`, `Row`, `Column`, `Box`, and `AnimatedVisibility` are Compose primitives, not Miuix components, so they do not get Miuix catalog rows or component docs. Use them to arrange and conditionally show Miuix components. Under `MiuixTheme`, standard Compose scrollables receive the theme's Miuix overscroll factory automatically; read `docs/guide/utils.md` when changing overscroll behavior.

### Scaffold

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| Scaffold | `docs/components/basic/scaffold.md` | `docs/demo/ScaffoldDemo.kt` | `miuix-ui/.../basic/Scaffold.kt` |

### Basic Components (miuix-ui)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| Surface | `docs/components/basic/surface.md` | `docs/demo/SurfaceDemo.kt` | `miuix-ui/.../basic/Surface.kt` |
| TopAppBar / SmallTopAppBar | `docs/components/basic/topappbar.md` | `docs/demo/TopAppBarDemo.kt` | `miuix-ui/.../basic/TopAppBar.kt` |
| NavigationBar | `docs/components/basic/navigationbar.md` | `docs/demo/NavigationBarDemo.kt` | `miuix-ui/.../basic/NavigationBar.kt` |
| FloatingNavigationBar | `docs/components/basic/navigationbar.md` | `docs/demo/NavigationBarDemo.kt` | `miuix-ui/.../basic/NavigationBar.kt` |
| NavigationRail | `docs/components/basic/navigationrail.md` | `docs/demo/NavigationRailDemo.kt` | `miuix-ui/.../basic/NavigationRail.kt` |
| TabRow | `docs/components/basic/tabrow.md` | `docs/demo/TabRowDemo.kt` | `miuix-ui/.../basic/TabRow.kt` |
| Card | `docs/components/basic/card.md` | `docs/demo/CardDemo.kt` | `miuix-ui/.../basic/Card.kt` |
| BasicComponent | `docs/components/basic/basiccomponent.md` | `docs/demo/BasicComponentDemo.kt` | `miuix-ui/.../basic/Component.kt` |
| Button | `docs/components/basic/button.md` | `docs/demo/ButtonDemo.kt` | `miuix-ui/.../basic/Button.kt` |
| IconButton | `docs/components/basic/iconbutton.md` | `docs/demo/IconButtonDemo.kt` | `miuix-ui/.../basic/IconButton.kt` |
| Text | `docs/components/basic/text.md` | `docs/demo/TextDemo.kt` | `miuix-ui/.../basic/Text.kt` |
| SmallTitle | `docs/components/basic/smalltitle.md` | `docs/demo/SmallTitleDemo.kt` | `miuix-ui/.../basic/SmallTitle.kt` |
| TextField | `docs/components/basic/textfield.md` | `docs/demo/TextFieldDemo.kt` | `miuix-ui/.../basic/TextField.kt` |
| Switch | `docs/components/basic/switch.md` | `docs/demo/SwitchDemo.kt` | `miuix-ui/.../basic/Switch.kt` |
| Checkbox | `docs/components/basic/checkbox.md` | `docs/demo/CheckboxDemo.kt` | `miuix-ui/.../basic/Checkbox.kt` |
| RadioButton | `docs/components/basic/radiobutton.md` | `docs/demo/RadioButtonDemo.kt` | `miuix-ui/.../basic/RadioButton.kt` |
| Slider | `docs/components/basic/slider.md` | `docs/demo/SliderDemo.kt` | `miuix-ui/.../basic/Slider.kt` |
| NumberPicker | `docs/components/basic/numberpicker.md` | `docs/demo/NumberPickerDemo.kt` | `miuix-ui/.../basic/NumberPicker.kt` |
| ProgressIndicator | `docs/components/basic/progressindicator.md` | `docs/demo/ProgressIndicatorDemo.kt` | `miuix-ui/.../basic/ProgressIndicator.kt` |
| Snackbar | `docs/components/basic/snackbar.md` | `docs/demo/SnackbarDemo.kt` | `miuix-ui/.../basic/Snackbar.kt` |
| Icon | `docs/components/basic/icon.md` | `docs/demo/IconDemo.kt` | `miuix-ui/.../basic/Icon.kt` |
| FloatingActionButton | `docs/components/basic/floatingactionbutton.md` | `docs/demo/FloatingActionButtonDemo.kt` | `miuix-ui/.../basic/FloatingActionButton.kt` |
| FloatingToolbar | `docs/components/basic/floatingtoolbar.md` | `docs/demo/FloatingToolbarDemo.kt` | `miuix-ui/.../basic/FloatingToolbar.kt` |
| BreadcrumbBar | `docs/components/basic/breadcrumbbar.md` | `docs/demo/BreadcrumbBarDemo.kt` | `miuix-ui/.../basic/BreadcrumbBar.kt` |
| Divider | `docs/components/basic/divider.md` | `docs/demo/DividerDemo.kt` | `miuix-ui/.../basic/Divider.kt` |
| PullToRefresh | `docs/components/basic/pulltorefresh.md` | `docs/demo/PullToRefreshDemo.kt` | `miuix-ui/.../basic/PullToRefresh.kt` |
| SearchBar | `docs/components/basic/searchbar.md` | `docs/demo/SearchBarDemo.kt` | `miuix-ui/.../basic/SearchBar.kt` |
| ColorPalette | `docs/components/basic/colorpalette.md` | `docs/demo/ColorPaletteDemo.kt` | `miuix-ui/.../basic/ColorPalette.kt` |
| ColorPicker | `docs/components/basic/colorpicker.md` | `docs/demo/ColorPickerDemo.kt` | `miuix-ui/.../basic/ColorPicker.kt` |
| VerticalScrollBar / HorizontalScrollBar | — (no dedicated doc/demo) | Integrated use: `example/shared/.../SettingsPage.kt`, `MainPage.kt` | `miuix-ui/.../basic/ScrollBar.kt` |
| Tooltip | `docs/components/basic/tooltip.md` | `docs/demo/TooltipDemo.kt` | `miuix-ui/.../basic/Tooltip.kt` |
| Badge | `docs/components/basic/badge.md` | `docs/demo/BadgeDemo.kt` | `miuix-ui/.../basic/Badge.kt` |

### Preference Components (miuix-preference)

| Component | Doc | Demo | Source | Contract |
|---|---|---|---|---|
| SwitchPreference | `docs/components/extended/switchpreference.md` | `docs/demo/SwitchPreferenceDemo.kt` | `miuix-preference/.../preference/SwitchPreference.kt` | `contracts/preferences-and-menus.md` |
| ArrowPreference | `docs/components/extended/arrowpreference.md` | `docs/demo/ArrowPreferenceDemo.kt` | `miuix-preference/.../preference/ArrowPreference.kt` | `contracts/preferences-and-menus.md` |
| CheckboxPreference | `docs/components/extended/checkboxpreference.md` | `docs/demo/CheckboxPreferenceDemo.kt` | `miuix-preference/.../preference/CheckboxPreference.kt` | `contracts/preferences-and-menus.md` |
| RadioButtonPreference | `docs/components/extended/radiobuttonpreference.md` | `docs/demo/RadioButtonPreferenceDemo.kt` | `miuix-preference/.../preference/RadioButtonPreference.kt` | `contracts/preferences-and-menus.md` |
| SliderPreference / RangeSliderPreference | `docs/components/extended/sliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` | `contracts/preferences-and-menus.md` |
| OverlayDropdownPreference / WindowDropdownPreference | `docs/components/extended/overlaydropdownpreference.md` | `docs/demo/OverlayDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/OverlayDropdownPreference.kt` | `contracts/preferences-and-menus.md` |
| OverlaySpinnerPreference / WindowSpinnerPreference | `docs/components/extended/overlayspinnerpreference.md` | `docs/demo/OverlaySpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/OverlaySpinnerPreference.kt` | `contracts/preferences-and-menus.md` |
| OverlayDropdownMenu / WindowDropdownMenu | `docs/components/extended/overlaydropdownmenu.md` | `docs/demo/OverlayDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayDropdownMenu.kt` | `contracts/preferences-and-menus.md` |
| OverlayIconDropdownMenu / WindowIconDropdownMenu | `docs/components/extended/overlayicondropdownmenu.md` | `docs/demo/OverlayIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconDropdownMenu.kt` | `contracts/preferences-and-menus.md` |
| OverlayIconCascadingDropdownMenu / WindowIconCascadingDropdownMenu | `docs/components/extended/overlayiconcascadingdropdownmenu.md` | `docs/demo/OverlayIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconCascadingDropdownMenu.kt` | `contracts/preferences-and-menus.md` |

### Glass Components (miuix-glass)

| Component | Doc | Demo | Source | Contract |
|---|---|---|---|---|
| GlassTopAppBar | `docs/components/glass/glasstopappbar.md` | `docs/demo/GlassTopAppBarDemo.kt` | `miuix-glass/.../glass/GlassTopAppBar.kt` | `contracts/glass.md` |
| GlassNavigationBar | `docs/components/glass/glassnavigationbar.md` | `docs/demo/GlassNavigationBarDemo.kt` | `miuix-glass/.../glass/GlassNavigationBar.kt` | `contracts/glass.md` |
| GlassNavigationRail | `docs/components/glass/glassnavigationrail.md` | `docs/demo/GlassNavigationRailDemo.kt` | `miuix-glass/.../glass/GlassNavigationRail.kt` | `contracts/glass.md` |
| GlassFloatingActionButton / GlassFloatingToolbar | `docs/components/glass/glassfloatingactionbutton.md`, `glassfloatingtoolbar.md` | matching `docs/demo/` files | `miuix-glass/.../glass/GlassFloatingActionButton.kt`, `GlassFloatingToolbar.kt` | `contracts/glass.md` |
| GlassTabRow / GlassSegmentedTabRow | `docs/components/glass/glasstabrow.md` | `docs/demo/GlassTabRowDemo.kt` | `miuix-glass/.../glass/GlassTabRow.kt` | `contracts/glass.md` |
| GlassIconButton | `docs/components/glass/glassiconbutton.md` | `docs/demo/GlassIconButtonDemo.kt` | `miuix-glass/.../glass/GlassIconButton.kt` | `contracts/glass.md` |
| GlassPopup / GlassPopupItem | `docs/components/glass/glasspopup.md` | `docs/demo/GlassPopupDemo.kt` | `miuix-glass/.../glass/GlassPopup.kt` | `contracts/glass.md` |
| GlassDropdownPopup | `docs/components/glass/glassdropdownpopup.md` | `docs/demo/GlassDropdownPopupDemo.kt` | `miuix-glass/.../glass/GlassDropdownPopup.kt` | `contracts/glass.md` |
| GlassIconDropdownMenu | No dedicated component page; integrated Example use | `example/shared/.../MainPage.kt` | `miuix-glass/.../glass/GlassIconDropdownMenu.kt` | `contracts/glass.md` |
| GlassTransformPopup / GlassSecondaryPopup | `docs/components/glass/glasstransformpopup.md`, `glasssecondarypopup.md` | matching `docs/demo/` files | `miuix-glass/.../glass/` | `contracts/glass.md` |
| GlassDialog / GlassDialogButton | `docs/components/glass/glassdialog.md` | `docs/demo/GlassDialogDemo.kt` | `miuix-glass/.../glass/GlassDialog.kt` | `contracts/glass.md` |
| GlassBottomSheet | `docs/components/glass/glassbottomsheet.md` | `docs/demo/GlassBottomSheetDemo.kt` | `miuix-glass/.../glass/GlassBottomSheet.kt` | `contracts/glass.md` |
| GlassDropdownPreference | `docs/components/glass/glassdropdownpreference.md` | `docs/demo/GlassDropdownPreferenceDemo.kt` | `miuix-glass/.../glass/GlassDropdownPreference.kt` | `contracts/glass.md` |

### Utilities (miuix-ui)

| Utility | Guide | Source |
|---|---|---|
| Pager gesture conflict and spring page navigation | `docs/guide/utils.md` | `miuix-ui/.../utils/PagerGestureUtils.kt` |

### Public API boundaries

The table above is a catalog of public entry points. Supporting source paths are evidence for behavior, not automatically importable APIs. In particular:

- `TextButton` is a public button API documented and implemented with the Button family; verify its exact source signature before treating it as a separate module or inventing a wrapper.
- `InputField` is the editable-field API nested in the SearchBar documentation/source. `SearchBar` itself does not expose every parameter accepted by `InputField`, including its color parameter.
- `ProgressiveBlur` is a public effect descriptor used by modifier APIs in `miuix-blur`, not a general-purpose `miuix-ui` component; verify the target platform and the exact modifier overload in the blur source.
- Names under [Supporting Internals](source-verification.md#supporting-internals), such as `ListPopupContent` or `DropdownImpl`, are implementation evidence unless the pinned source exports them publicly.

### SmallTitle and section labels

At the current source-audit snapshot there is no public Miuix `SectionHeader`. `SmallTitle` is the library's auxiliary/category label and the Example normally places it immediately before the related `Card`, not inside the Card as another preference row. If a target project has a `SectionHeader`, inspect it as an application-owned wrapper before replacing it.

### Scrollbar integration

`VerticalScrollBar` and `HorizontalScrollBar` are experimental (`ExperimentalScrollBarApi`). Pair the bar with the same `ScrollState`, `LazyListState`, or `LazyGridState` used by the scrollable through `rememberScrollBarAdapter(state)`. The Example overlays a vertical bar as a sibling of `LazyColumn` inside a `Box`, aligns it to the end edge, and forwards the list's `contentPadding` as `trackPadding`; this keeps the bar from becoming list content or covering padded regions. Prefer this pattern for long desktop/tablet-style content, and omit a visible scrollbar when the product does not need one.

### TextField validation

`TextField` has no `isError` or `supportingText` parameter at the current source-audit snapshot. For field, Preference-row, transient-network, and blocking-page failure composition, consult the `miuix-guidelines` Skill rather than inferring Material parameters.

### Current snapshot additions and behavior notes

- `Scaffold` hosts `floatingToolbar` and `floatingToolbarPosition`; use the scaffold slot instead of manually overlaying a toolbar, and rely on its snackbar spacing for a bottom toolbar.
- `InputField` accepts `color`; the `SearchBar` wrapper does not. Pass `Color.Transparent` only when another surface, such as a backdrop blur, intentionally owns the capsule background.
- `PullToRefresh` supports `rememberPullToRefreshState(refreshThreshold = ...)` and `onPullProgress`; keep one state owner for a refresh interaction instead of deriving a second refresh state. For exact signatures and mixed top-bar/overscroll topology, read [Scroll, pull-to-refresh, and overscroll contracts](contracts/scroll-refresh-and-overscroll.md).
- `NavigationRail` has a fixed-layout overload (`expanded = false/true`) and a separate expandable overload with required non-null `state`; `state = null` is not a valid current call shape.
- `NavigationBarItem` and `FloatingNavigationBarItem` share `NavigationBarItemColors` through `NavigationBarDefaults.navigationBarItemColors()`; `colors` appears before `badge`; use named arguments for these options.
- `TabRow` and `TabRowWithContour` contain horizontal nested scroll/overscroll. For a pager containing vertical lists, use the dedicated `PagerGestureUtils` configuration instead of layering an ad-hoc nested-scroll interceptor.
- `PagerInterceptionMode` has only `Native` and `CrossAxis`. Cross-Axis requires the same `flingBehavior` on `Modifier.pagerGestureOverride` and `HorizontalPager`, `userScrollEnabled = false`, and `PagerGestureNestedScrollConnection`.
- For an exact pager configuration or `BreadcrumbBar(listState = ...)`, use the [Pager and breadcrumb contract](contracts/pager-and-breadcrumb.md). The breadcrumb row uses its own `LazyListState`.
- Glass components are an optional family, not aliases for the basic components. Read [Glass components](glass.md) for the `Backdrop` recording boundary. `GlassDialog`, `GlassBottomSheet`, and `GlassDropdownPreference` use the `Scaffold` popup host; the `GlassPopup`/`GlassTransformPopup`/`GlassIconDropdownMenu` family is composed in the caller's root `BoxScope`.
- `LocalNavTransitionScope.current` is available only inside a `NavDisplay` entry; use its `isRunning` signal for coarse lifecycle decisions and deferred scope properties for frame-level effects.
