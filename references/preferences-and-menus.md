# Preference and Dropdown Menu Components

## Choose the Family

| User need | Prefer | Why |
|---|---|---|
| Binary/value control inside a form or custom layout | Basic `Switch`, `Checkbox`, `RadioButton`, or `Slider` | The surrounding layout owns the label and grouping |
| Complete settings row with title, summary, and row interaction | Matching `*Preference` component | The component owns HyperOS-style row structure, enabled state, and trailing control/value |
| Plain text option selection in a settings row | `OverlayDropdownPreference` or `WindowDropdownPreference` | Direct `List<String>` + selected-index API and current-value display |
| Rich options with icons/summaries, or spinner dialog mode | `OverlaySpinnerPreference` or `WindowSpinnerPreference` | Uses richer `DropdownItem` content and spinner-specific presentation |
| Standalone anchored action menu rather than a settings row | `*DropdownMenu` family | The caller owns the anchor and menu action semantics |

Choose Overlay versus Window lifetime with [Overlay and Window Components](overlays-and-windows.md#choose-the-host-first). For settings-page grouping and state ownership, continue with [Example-derived usage patterns](usage-patterns.md#settings-and-grouped-content). Verify the chosen row's exact call shape in its doc/demo/source through [Source verification](source-verification.md).

## Choose a Menu Variant

| Need | Choose |
|---|---|
| A settings row that displays one current value and owns a selected index | `*DropdownPreference` or richer `*SpinnerPreference`, not a menu |
| A full `BasicComponent`-style row that opens flat actions, grouped choices, or multi-select items | `OverlayDropdownMenu` / `WindowDropdownMenu` |
| A compact toolbar/action icon that opens the same flat menu model | `OverlayIconDropdownMenu` / `WindowIconDropdownMenu` |
| An icon action whose top-level items open child choices | `OverlayIconCascadingDropdownMenu` / `WindowIconCascadingDropdownMenu`; `v0.9.3` supports at most two levels |

“Icon” describes the anchor (`IconButton`) rather than promising an icon on every menu item. Plain menu items may still expose selection state. Choose Overlay versus Window after choosing flat versus cascading and row versus icon anchor.

## Preference Components (miuix-preference)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| ArrowPreference | `docs/components/arrowpreference.md` | `docs/demo/ArrowPreferenceDemo.kt` | `miuix-preference/.../preference/ArrowPreference.kt` |
| SwitchPreference | `docs/components/switchpreference.md` | `docs/demo/SwitchPreferenceDemo.kt` | `miuix-preference/.../preference/SwitchPreference.kt` |
| CheckboxPreference | `docs/components/checkboxpreference.md` | `docs/demo/CheckboxPreferenceDemo.kt` | `miuix-preference/.../preference/CheckboxPreference.kt` |
| RadioButtonPreference | `docs/components/radiobuttonpreference.md` | `docs/demo/RadioButtonPreferenceDemo.kt` | `miuix-preference/.../preference/RadioButtonPreference.kt` |
| OverlayDropdownPreference | `docs/components/overlaydropdownpreference.md` | `docs/demo/OverlayDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/OverlayDropdownPreference.kt` |
| OverlaySpinnerPreference | `docs/components/overlayspinnerpreference.md` | `docs/demo/OverlaySpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/OverlaySpinnerPreference.kt` |
| WindowDropdownPreference | `docs/components/windowdropdownpreference.md` | `docs/demo/WindowDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/WindowDropdownPreference.kt` |
| WindowSpinnerPreference | `docs/components/windowspinnerpreference.md` | `docs/demo/WindowSpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/WindowSpinnerPreference.kt` |
| SliderPreference | `docs/components/sliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |
| RangeSliderPreference | `docs/components/rangesliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |

### RangeSliderPreference quick cue

There is no dedicated `docs/demo/` file, but the tagged component doc contains complete examples. It is the settings-row counterpart for selecting two related values, with `value` + `onValueChange` as the controlled state; the most common additional parameters are `valueRange`, `steps`, `enabled`, `summary`, and optional `valueText`. Read the shared `SliderPreference.kt` source when using key points, magnetic snapping, haptics, or custom slider colors.

## Enabled and Dependent States

- Public Preference components expose `enabled`. With `enabled = false`, the row is not clickable, built-in trailing controls such as the `Switch` are also disabled, and title/summary/action colors resolve through disabled roles. The controlled value remains the caller's state and should not be reset merely because the row is disabled.
- Pass the same prerequisite to every interactive child you supply yourself. A custom `startAction`, `endActions`, or `bottomAction` slot cannot infer how its nested buttons should be disabled.
- Use `AnimatedVisibility` when the setting is irrelevant or the capability is unavailable. Keep the row visible with `enabled = false` when discoverability matters, and explain the prerequisite in `summary`.
- For linked settings, derive child `enabled`/visibility from the parent value while hoisting both values to the same owner. Do not duplicate a second local Boolean inside the Preference.

## Dropdown Menu Components (miuix-preference/menu/)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDropdownMenu | `docs/components/overlaydropdownmenu.md` | `docs/demo/OverlayDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayDropdownMenu.kt` |
| OverlayIconDropdownMenu | `docs/components/overlayicondropdownmenu.md` | `docs/demo/OverlayIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconDropdownMenu.kt` |
| OverlayIconCascadingDropdownMenu | `docs/components/overlayiconcascadingdropdownmenu.md` | `docs/demo/OverlayIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconCascadingDropdownMenu.kt` |
| WindowDropdownMenu | `docs/components/windowdropdownmenu.md` | `docs/demo/WindowDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowDropdownMenu.kt` |
| WindowIconDropdownMenu | `docs/components/windowicondropdownmenu.md` | `docs/demo/WindowIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconDropdownMenu.kt` |
| WindowIconCascadingDropdownMenu | `docs/components/windowiconcascadingdropdownmenu.md` | `docs/demo/WindowIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconCascadingDropdownMenu.kt` |
