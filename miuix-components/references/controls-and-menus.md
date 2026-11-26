# Controls, Inputs, Preferences, and Menus

Read official docs at pinned `0.9.5-rc01` (`docs/components/basic/...`, `docs/components/extended/...`) for full parameter tables; this reference retains selection boundaries, overload traps, and host contracts.

Contents: [buttons & actions](#buttons-and-floating-actions) · [TextField](#textfield) · [progress](#progress-indicators) · [controls & pickers](#selection-controls-and-pickers) · [search](#searchbar-inputfield-and-searchclearbutton) · [preference vs menu](#choose-a-preference-or-menu-family) · [preference & menu contracts](#preference-and-menu-contracts)

## Buttons and Floating Actions

- `Button` / `TextButton`: `docs/components/basic/button.md` (`top.yukonga.miuix.kmp.basic`). **`ButtonDefaults` owns BOTH**: pass `colors = ButtonDefaults.buttonColors(color = ...)` or `.buttonColorsPrimary()` to `Button`, and `ButtonDefaults.textButtonColors()` / `.textButtonColorsPrimary()` to `TextButton` (parameter is `color`, not `containerColor`, and there is no `TextButtonDefaults`).
- `IconButton`: `docs/components/basic/iconbutton.md` (`IconButton.kt`); owns button semantics and 40 dp default touch target.
- `FloatingActionButton` / `FloatingToolbar`: `docs/components/basic/floatingactionbutton.md`, `floatingtoolbar.md`. Put inside `Scaffold`'s `floatingActionButton` / `floatingToolbar` slots so Snackbar clearance applies automatically. Note `FloatingActionButton` defaults `containerColor` to `primary` while inner `Surface` content defaults to `onSurface` (explicitly set `Icon(tint = MiuixTheme.colorScheme.onPrimary)` when needed).

## TextField

Doc: `docs/components/basic/textfield.md` (`miuix-ui/.../basic/TextField.kt`).

- `TextField` has **no** `placeholder`, `isError`, or `supportingText` parameter; compose validation styling/messages externally.
- `TextField(state = rememberTextFieldState(...))` (`androidx.compose.foundation.text.input.rememberTextFieldState`) is the `rc01` canonical overload (`InputTransformation`, `OutputTransformation`, `TextFieldLineLimits`, `onKeyboardAction`). Value-based `value: String` / `value: TextFieldValue` overloads are deprecated.

```kotlin
val state = rememberTextFieldState()
TextField(
    state = state,
    label = "Name",
    useLabelAsPlaceholder = true, // Moves/shows label as placeholder when empty
    lineLimits = TextFieldLineLimits.SingleLine,
)
```

## Progress Indicators

Doc: `docs/components/basic/progressindicator.md` (`ProgressIndicator.kt`).

- Both `LinearProgressIndicator` and `CircularProgressIndicator` provide `progress: () -> Float` overloads for draw-phase reads during high-frequency animation, alongside static `Float` / nullable indeterminate overloads.

## Selection Controls and Pickers

- `Switch` (`docs/components/basic/switch.md`): thumb dragging uses `folmeSpring` position tracking with drag-cancellation arbitration.
- `Checkbox` (`docs/components/basic/checkbox.md`): takes **`state: ToggleableState` and `onClick: (() -> Unit)?`**, not `checked: Boolean` / `onCheckedChange`. Pass `ToggleableState(checked)` for binary toggles (whereas `CheckboxPreference` takes `checked: Boolean`).
- `RadioButton` (`docs/components/basic/radiobutton.md`).
- `Slider` / `VerticalSlider` / `RangeSlider` (`docs/components/basic/slider.md`): support **tap-to-seek** gestures in addition to dragging.
- `NumberPicker` (`docs/components/basic/numberpicker.md`): `visibleItemCount` must be **odd and `>= 3`** (default `5`); `range` must not be empty.

## SearchBar, InputField, and SearchClearButton

Doc: `docs/components/basic/searchbar.md` (`SearchBar.kt`, `SearchClearButton.kt`).

- `InputField` accepts `color`; the outer `SearchBar` wrapper does not. Pass `Color.Transparent` to `InputField` only when another container intentionally paints/blurs the capsule background.
- `SearchClearButton` (`top.yukonga.miuix.kmp.basic`) is publicly exported for search clear buttons.

```kotlin
var query by rememberSaveable { mutableStateOf("") }
var expanded by rememberSaveable { mutableStateOf(false) }

SearchBar(
    inputField = {
        InputField(
            query = query, onQueryChange = { query = it },
            onSearch = { /* Search */ },
            expanded = expanded, onExpandedChange = { expanded = it },
            label = "Search",
        )
    },
    expanded = expanded,
    onExpandedChange = { expanded = it },
) { /* Expanded results */ }
```

## Choose a Preference or Menu Family

| User need | Choose | Why |
|---|---|---|
| Bare control in custom layout | `Switch` / `Checkbox` / `RadioButton` / `Slider` | Surrounding layout owns label & containment |
| Complete HyperOS settings row | `*Preference` (`miuix-preference`) | Owns title/summary/disabled visuals & trailing control |
| Plain-string selector row | `OverlayDropdownPreference` / `WindowDropdownPreference` | Takes `List<String>` + `selectedIndex` |
| Rich selector row (icon/summary) | `OverlaySpinnerPreference` / `WindowSpinnerPreference` | Takes `DropdownItem`; `rc01` has no `dialogMode` flag |
| Glass selector row | `GlassDropdownPreference` (`miuix-glass`) | Requires `Backdrop` + `Scaffold` popup host; string items only |
| Anchored action/multi-choice menu | `*DropdownMenu` family (`miuix-preference/.../menu/`) | Triggered from a row or `IconButton` anchor; not a setting row |
| Cascading icon submenu | `*IconCascadingDropdownMenu` | Top-level items with `DropdownItem.children` open child submenus |

Choose `Overlay*` (requires `Scaffold` popup host; respects `renderInRootScaffold`) vs `Window*` (standalone platform window layer, omits `renderInRootScaffold`) first; see [Overlays & windows](overlays-and-windows.md).

## Preference and Menu Contracts

- **Preference docs (`docs/components/extended/`)**: `switchpreference.md`, `arrowpreference.md`, `checkboxpreference.md`, `radiobuttonpreference.md`, `sliderpreference.md`, `rangesliderpreference.md`, `overlaydropdownpreference.md`, `windowdropdownpreference.md`, `overlayspinnerpreference.md`, `windowspinnerpreference.md`.
- **Menu docs (`docs/components/extended/`)**: `overlaydropdownmenu.md`, `windowdropdownmenu.md`, `overlayicondropdownmenu.md`, `windowicondropdownmenu.md`, `overlayiconcascadingdropdownmenu.md`, `windowiconcascadingdropdownmenu.md`.
- **`RadioButtonPreference`**: Hoist `selected` + `onClick`; pass `colors = RadioButtonPreferenceColors` (from `RadioButtonPreferenceDefaults.radioButtonPreferenceColors()`; selected title/summary default to `primary`, no separate `titleColor`/`summaryColor` args); supports `radioButtonLocation = RadioButtonLocation.Start` / `.End`. The whole row owns selection/haptics — do not wrap the glyph in a second `clickable`.
- **Slots & dependent settings (`startAction`, `endActions`, `bottomAction`)**: Use Miuix row slots instead of Material `leadingContent`/`trailingContent`. `SwitchPreference` / `ArrowPreference` default `endActions: @Composable RowScope.() -> Unit = EmptyEndActions` (non-nullable), whereas `BasicComponent`, `CheckboxPreference`, `RadioButtonPreference`, and `SliderPreference` accept nullable `((RowScope.() -> Unit)?) = null`. Setting `enabled = false` disables row clicks and built-in trailing controls while keeping controlled state intact, but custom `startAction`/`endActions`/`bottomAction` interactive children must receive `enabled` explicitly.
- **Self-contained Dropdown/Spinner menus & rows (no `expanded` input)**: `OverlayDropdownMenu` / `WindowDropdownMenu`, `OverlayIconDropdownMenu` / `WindowIconDropdownMenu`, `*DropdownPreference`, and `*SpinnerPreference` own `isDropdownExpanded` state internally. Never wrap them in an M3 `DropdownMenu(expanded = ...)` or pass an `expanded` input parameter — supply `items` or `entry` / `entries`, plus optional `onExpandedChange` only to observe state changes:

```kotlin
var sortIndex by remember { mutableIntStateOf(0) }
val sortEntry = DropdownEntry(
    items = listOf("Name", "Date", "Size").mapIndexed { index, label ->
        DropdownItem(text = label, selected = sortIndex == index, onClick = { sortIndex = index })
    },
)
// Inside SmallTopAppBar actions = { ... } (Scaffold popup host present):
OverlayIconDropdownMenu(entry = sortEntry, collapseOnSelection = true) {
    Icon(imageVector = MiuixIcons.Basic.Sidebar, contentDescription = "Sort")
}
```

- **Cascading & Dropdown models (`DropdownEntry`, `DropdownItem` in `.basic`)**:
  - `DropdownEntry` and `DropdownItem` are regular `@Immutable` classes (with manual `equals`/`hashCode`/`toString`/`copy`/`componentN` and `.toList()` copies) rather than Kotlin `data class` bytecode.
  - Cascading state tracks entry/item **index position** rather than object reference so selection updates don't close the menu, meaning item ordering must remain stable while the popup is shown; predictive back can interrupt/reverse expand transitions.
  - On `OverlayIconCascadingDropdownMenu` / `WindowIconCascadingDropdownMenu`, the surface-taking overload inserts `surface: (@Composable (Shape) -> Modifier)?` right after `entry` (check overload signatures before merging params).
  - Lower-level `OverlayDropdownPopup`, `OverlayDropdownDialog`, `WindowDropdownPopup`, and `WindowDropdownDialog` (`miuix-preference/.../popup/`) place `onDismissFinished: (() -> Unit)? = null` as the **last** parameter in `rc01`.
