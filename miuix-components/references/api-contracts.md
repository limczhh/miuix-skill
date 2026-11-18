# Verified API Contract Index

Use when the symbol is known but its contract section is not. Open the linked section only; skip this index when the entrypoint already gives the right link. Contracts target `v0.9.5-beta01` / `6d2554a850b8960e2616c968263ff64beb5ffb03`.

## Select one contract guide

| Need | Section |
|---|---|
| Theme / Scaffold | [Theme](contracts/theme-and-host.md#theme) / [page host](contracts/theme-and-host.md#page-host) |
| TopAppBar / SmallTopAppBar / custom title layout | [Top bar](contracts/top-bars-and-scroll.md#top-app-bar) / [low-level layout](contracts/top-bars-and-scroll.md#low-level-title-layout) |
| ScrollBehavior / PullToRefresh / overscroll bridge | [Collapsing behavior](contracts/top-bars-and-scroll.md#collapsing-top-bar-behavior) / [refresh declarations](contracts/scroll-refresh-and-overscroll.md#public-declarations) / [integration topology](contracts/scroll-refresh-and-overscroll.md#choose-one-top-bar-integration-topology) |
| Pager / BreadcrumbBar | [Pager](contracts/pager-and-breadcrumb.md#pager) / [breadcrumb](contracts/pager-and-breadcrumb.md#breadcrumb) |
| Card / SmallTitle / navigation bars | [Surface and navigation sections](contracts/surfaces-and-navigation.md) |
| Preference row / dropdown / menu | [Preference and menu sections](#preferences-and-menus) |
| Basic control / input / button | [Input and action sections](#inputs-and-actions) |
| Dialog / sheet / popup / Snackbar | [Overlay sections](#overlays) |
| Glass component / surface modifier | [Glass sections](#glass) |

## Preferences and menus

| Symbol family | Section |
|---|---|
| SwitchPreference / ArrowPreference | [Switch](contracts/preferences-and-menus.md#switchpreference) / [arrow](contracts/preferences-and-menus.md#arrowpreference) |
| CheckboxPreference / RadioButtonPreference | [Checkbox](contracts/preferences-and-menus.md#checkboxpreference) / [radio](contracts/preferences-and-menus.md#radiobuttonpreference) |
| SliderPreference / RangeSliderPreference | [Slider](contracts/preferences-and-menus.md#sliderpreference) / [range](contracts/preferences-and-menus.md#rangesliderpreference) |
| Dropdown models / string-list preferences | [Models](contracts/preferences-and-menus.md#dropdown-models) / [dropdown preference](contracts/preferences-and-menus.md#overlaydropdownpreference--windowdropdownpreference-string-list) |
| Rich spinner preferences | [Spinner](contracts/preferences-and-menus.md#overlayspinnerpreference--windowspinnerpreference-rich-dropdownitem) |
| Row / icon dropdown menus | [Row anchor](contracts/preferences-and-menus.md#overlaydropdownmenu--windowdropdownmenu-row-anchor) / [icon anchor](contracts/preferences-and-menus.md#overlayicondropdownmenu--windowicondropdownmenu-iconbutton-anchor) |
| Icon cascading menu / surface overload | [Cascading menu](contracts/preferences-and-menus.md#overlayiconcascadingdropdownmenu--windowiconcascadingdropdownmenu-submenu-cascading) |

## Inputs and actions

| Symbol family | Section |
|---|---|
| Button / TextButton / IconButton | [Text buttons](contracts/inputs-and-actions.md#button--textbutton) / [icon button](contracts/inputs-and-actions.md#iconbutton) |
| FloatingActionButton / FloatingToolbar | [Floating actions](contracts/inputs-and-actions.md#floatingactionbutton--floatingtoolbar) |
| TextField | [Field](contracts/inputs-and-actions.md#text-field) |
| Switch / Checkbox / RadioButton | [Switch](contracts/inputs-and-actions.md#switch) / [checkbox](contracts/inputs-and-actions.md#checkbox) / [radio](contracts/inputs-and-actions.md#radiobutton) |
| Slider / RangeSlider | [Slider](contracts/inputs-and-actions.md#slider) / [range](contracts/inputs-and-actions.md#rangeslider) |
| NumberPicker / SearchBar / InputField | [Picker](contracts/inputs-and-actions.md#numberpicker) / [search and field](contracts/inputs-and-actions.md#searchbar--inputfield) |

## Overlays

| Symbol family | Section |
|---|---|
| OverlayDialog / WindowDialog | [Dialogs](contracts/overlays.md#overlaydialog--windowdialog) |
| OverlayBottomSheet / WindowBottomSheet | [Sheets](contracts/overlays.md#overlaybottomsheet--windowbottomsheet) |
| OverlayListPopup / WindowListPopup | [List popups](contracts/overlays.md#list-popups) |
| Snackbar / SnackbarHostState | [Snackbars](contracts/overlays.md#snackbars) |

## Glass

| Symbol family | Section |
|---|---|
| Backdrop recording | [Sampling](contracts/glass.md#sampling-primitives) |
| GlassTopAppBar | [Behavior-driven](contracts/glass.md#overload-1-scrollbehavior-driven) / [explicit overlap](contracts/glass.md#overload-2-explicit-iscontentscrolled-state) |
| GlassNavigationBar / GlassNavigationRail | [Bar](contracts/glass.md#glassnavigationbar) / [rail](contracts/glass.md#glassnavigationrail) |
| GlassTabRow / GlassSegmentedTabRow | [Tabs](contracts/glass.md#glasstabrow) / [segmented tabs](contracts/glass.md#glasssegmentedtabrow) |
| GlassDialog / GlassDialogButton | [Dialog](contracts/glass.md#glassdialog) / [dialog button](contracts/glass.md#glassdialogbutton) |
| glassDialogSurface / glassBottomSheetSurface | [Typed surface adapters](contracts/glass.md#typed-surface-adapters-for-standard-overlays) |
| GlassBottomSheet | [Sheet](contracts/glass.md#glassbottomsheet) |
| GlassDropdownPreference | [Preference](contracts/glass.md#glassdropdownpreference-scaffold-hosted) |
| GlassPopup / GlassDropdownPopup / GlassTransformPopup | [BoxScope popups](contracts/glass.md#boxscope-glass-popup-components) |
| GlassIconButton | [Icon button](contracts/glass.md#glassiconbutton) |
| GlassFloatingActionButton / GlassFloatingToolbar | [Floating actions](contracts/glass.md#glassfloatingactionbutton--glassfloatingtoolbar) |
| glassPanel / glass | [Surface modifiers](contracts/glass.md#low-level-glass-panel-modifiers) |

## Contract rules

Use the [covered-contract path](source-verification.md#covered-contract-path) for covered standard-beta01 calls. Inspect source only for a gap, override or contradiction. Declarations control library facts; usage examples and this router do not add parameters or defaults. Mark an intentional declaration excerpt as partial rather than suggesting it is complete.

For navigation runtime use [miuix-nav](miuix-nav.md). For an uncovered symbol use [source verification](source-verification.md#acquire-source-evidence), locating its source through [library map](library-map.md) only if necessary. Extend a contract for a demonstrated integration need, not as an exhaustive library dump.
