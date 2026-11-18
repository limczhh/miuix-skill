# Preference and Dropdown Menu Components

Use this reference to choose between Preference rows, basic controls, dropdown/spinner preferences, anchored menus, and the optional Glass dropdown row. For verified function signatures, parameter defaults, and data models, see [Preference and Dropdown Menu Contracts](contracts/preferences-and-menus.md).

## Choose the Family

| User need | Prefer | Why |
|---|---|---|
| Binary/value control inside a form or custom layout | Basic `Switch`, `Checkbox`, `RadioButton`, or `Slider` | The surrounding layout owns the label and grouping |
| Complete settings row with title, summary, and row interaction | Matching `*Preference` component | The component owns HyperOS-style row structure, enabled state, and trailing control/value |
| Plain text option selection in a settings row | `OverlayDropdownPreference` or `WindowDropdownPreference` | Direct `List<String>` + selected-index API and current-value display |
| Rich options with icons/summaries | `OverlaySpinnerPreference` or `WindowSpinnerPreference` | Uses richer `DropdownItem` content; beta01 has no `dialogMode` parameter. A requested selection dialog is a separate dialog composition |
| The same selection row must use the optional Glass visual family | `GlassDropdownPreference` | It is supplied by `miuix-glass`, samples a `Backdrop`, and still requires a `Scaffold` popup host; it is not a drop-in alias for every preference |
| Standalone anchored action menu rather than a settings row | `*DropdownMenu` family | The caller owns the anchor and menu action semantics |

Choose Overlay versus Window lifetime only when that is part of the request. Verify an exact call only when the selected component is not already covered by the API contract sheet.

## Choose a Menu Variant

| Need | Choose |
|---|---|
| A settings row that displays one current value and owns a selected index | `*DropdownPreference` or richer `*SpinnerPreference`, not a menu |
| A full `BasicComponent`-style row that opens flat actions, grouped choices, or multi-select items | `OverlayDropdownMenu` / `WindowDropdownMenu` |
| A compact toolbar/action icon that opens the same flat menu model | `OverlayIconDropdownMenu` / `WindowIconDropdownMenu` |
| An icon action whose top-level items open child choices | `OverlayIconCascadingDropdownMenu` / `WindowIconCascadingDropdownMenu`; verify the pinned source before relying on a nesting-depth limit |

“Icon” describes the anchor (`IconButton`) rather than promising an icon on every menu item. Plain menu items may still expose selection state. Choose Overlay versus Window after choosing flat versus cascading and row versus icon anchor.

## Preference Components (miuix-preference)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| ArrowPreference | `docs/components/extended/arrowpreference.md` | `docs/demo/ArrowPreferenceDemo.kt` | `miuix-preference/.../preference/ArrowPreference.kt` |
| SwitchPreference | `docs/components/extended/switchpreference.md` | `docs/demo/SwitchPreferenceDemo.kt` | `miuix-preference/.../preference/SwitchPreference.kt` |
| CheckboxPreference | `docs/components/extended/checkboxpreference.md` | `docs/demo/CheckboxPreferenceDemo.kt` | `miuix-preference/.../preference/CheckboxPreference.kt` |
| RadioButtonPreference | `docs/components/extended/radiobuttonpreference.md` | `docs/demo/RadioButtonPreferenceDemo.kt` | `miuix-preference/.../preference/RadioButtonPreference.kt` |
| OverlayDropdownPreference | `docs/components/extended/overlaydropdownpreference.md` | `docs/demo/OverlayDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/OverlayDropdownPreference.kt` |
| OverlaySpinnerPreference | `docs/components/extended/overlayspinnerpreference.md` | `docs/demo/OverlaySpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/OverlaySpinnerPreference.kt` |
| WindowDropdownPreference | `docs/components/extended/windowdropdownpreference.md` | `docs/demo/WindowDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/WindowDropdownPreference.kt` |
| WindowSpinnerPreference | `docs/components/extended/windowspinnerpreference.md` | `docs/demo/WindowSpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/WindowSpinnerPreference.kt` |
| SliderPreference | `docs/components/extended/sliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |
| RangeSliderPreference | `docs/components/extended/rangesliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |

### Glass selection row (`miuix-glass`)

| Component | Doc | Demo | Source |
|---|---|---|---|
| GlassDropdownPreference | `docs/components/glass/glassdropdownpreference.md` | `docs/demo/GlassDropdownPreferenceDemo.kt` | `miuix-glass/.../glass/GlassDropdownPreference.kt` |

`GlassDropdownPreference` has required `onSelectedIndexChange`, `items`, `selectedIndex`, `title`, and `backdrop` parameters. The caller owns selection; the component owns expansion/dismissal. The row is disabled when `enabled = false` or `items` is empty. For the backdrop recording and host contract, read [Glass components](glass.md) rather than treating it as a visual parameter added to the standard preference APIs.

### RadioButtonPreference in the current source snapshot

`RadioButtonPreference` is a controlled row: hoist `selected` and update it from `onClick`. Provide title and summary colors through `colors = RadioButtonPreferenceDefaults.radioButtonPreferenceColors(...)`; selected title and summary colors default to the theme primary color. The entire row owns the radio interaction and haptic feedback, so do not add a second clickable wrapper around the radio control.

Use `radioButtonLocation = RadioButtonLocation.Start` or `.End` when the control position matters. `startAction`, nullable `endActions`, and `bottomAction` are extension slots, and custom interactive content must receive the same `enabled` state explicitly.

The current cascading menu state is tracked by entry/item position rather than the `DropdownItem` object reference. This lets selected-state values refresh without closing the submenu, but requires the entry and item order to remain stable while the popup is visible. Predictive back can also interrupt and reverse the active enter/expand track.

### RangeSliderPreference quick cue

There is no dedicated `docs/demo/` file, but the upstream component doc contains complete examples. It is the settings-row counterpart for selecting two related values, with `value` + `onValueChange` as the controlled state; the most common additional parameters are `valueRange`, `steps`, `enabled`, `summary`, and optional `valueText`. Read the shared `SliderPreference.kt` source when using key points, magnetic snapping, haptics, or custom slider colors.

## Enabled and Dependent States

- Public Preference components expose `enabled`. With `enabled = false`, the row is not clickable, built-in trailing controls such as the `Switch` are also disabled, and title/summary/action colors resolve through disabled roles. The controlled value remains the caller's state and should not be reset merely because the row is disabled.
- Pass the same prerequisite to every interactive child you supply yourself. A custom `startAction`, `endActions`, or `bottomAction` slot cannot infer how its nested buttons should be disabled.
- Use `AnimatedVisibility` when the setting is irrelevant or the capability is unavailable. Keep the row visible with `enabled = false` when discoverability matters, and explain the prerequisite in `summary`.
- For linked settings, derive child `enabled`/visibility from the parent value while hoisting both values to the same owner. Do not duplicate a second local Boolean inside the Preference.

## Dropdown Menu Components (miuix-preference/menu/)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDropdownMenu | `docs/components/extended/overlaydropdownmenu.md` | `docs/demo/OverlayDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayDropdownMenu.kt` |
| OverlayIconDropdownMenu | `docs/components/extended/overlayicondropdownmenu.md` | `docs/demo/OverlayIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconDropdownMenu.kt` |
| OverlayIconCascadingDropdownMenu | `docs/components/extended/overlayiconcascadingdropdownmenu.md` | `docs/demo/OverlayIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconCascadingDropdownMenu.kt` |
| WindowDropdownMenu | `docs/components/extended/windowdropdownmenu.md` | `docs/demo/WindowDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowDropdownMenu.kt` |
| WindowIconDropdownMenu | `docs/components/extended/windowicondropdownmenu.md` | `docs/demo/WindowIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconDropdownMenu.kt` |
| WindowIconCascadingDropdownMenu | `docs/components/extended/windowiconcascadingdropdownmenu.md` | `docs/demo/WindowIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconCascadingDropdownMenu.kt` |
