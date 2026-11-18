# Preference and Dropdown Menu Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta01 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

These families belong to `miuix-preference`: rows use `top.yukonga.miuix.kmp.preference`, menu wrappers use `.menu`, while `DropdownItem` / `DropdownEntry` use `.basic`. Overlay variants use the Miuix Scaffold popup host; Window variants do not. Choose the model and host through [preference/menu decisions](../preferences-and-menus.md) rather than transferring arguments between wrappers.

## Standard Preference Rows

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/`

### SwitchPreference

Read the pinned documentation/source: [SwitchPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/switchpreference.md).

### ArrowPreference

Read the pinned documentation/source: [ArrowPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/arrowpreference.md).

### CheckboxPreference

Read the pinned documentation/source: [CheckboxPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/checkboxpreference.md).

### RadioButtonPreference

Read the pinned documentation/source: [RadioButtonPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/radiobuttonpreference.md).
> [!NOTE]
> `RadioButtonPreference` uses `colors: RadioButtonPreferenceColors` instead of separate `titleColor`/`summaryColor` parameters.

---

## Slider Preferences

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/SliderPreference.kt`

### SliderPreference

Read the pinned documentation/source: [SliderPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/sliderpreference.md).

### RangeSliderPreference

Read the pinned documentation/source: [RangeSliderPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/rangesliderpreference.md).

---

## Dropdown & Spinner Preferences

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/`

### Dropdown Models

Read the pinned documentation/source: [DropdownItem / DropdownEntry](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/overlaydropdownpreference.md).

### OverlayDropdownPreference & WindowDropdownPreference (String List)

Read the pinned documentation/source: [OverlayDropdownPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/overlaydropdownpreference.md), [WindowDropdownPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/windowdropdownpreference.md).

### OverlaySpinnerPreference & WindowSpinnerPreference (Rich DropdownItem)

Read the pinned documentation/source: [OverlaySpinnerPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/overlayspinnerpreference.md), [WindowSpinnerPreference](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/windowspinnerpreference.md).

---

## Anchored Dropdown Menus

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/menu/`

### OverlayDropdownMenu & WindowDropdownMenu (Row Anchor)

Read the pinned documentation/source: [OverlayDropdownMenu](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/overlaydropdownmenu.md).

### OverlayIconDropdownMenu & WindowIconDropdownMenu (IconButton Anchor)

Read the pinned documentation/source: [OverlayIconDropdownMenu](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/overlayicondropdownmenu.md).

### OverlayIconCascadingDropdownMenu & WindowIconCascadingDropdownMenu (Submenu Cascading)

Read the pinned documentation/source: [OverlayIconCascadingDropdownMenu](https://raw.githubusercontent.com/compose-miuix-ui/miuix/6d2554a850b8960e2616c968263ff64beb5ffb03/docs/components/extended/overlayiconcascadingdropdownmenu.md).
The surface-taking overload adds required `surface: (@Composable (Shape) -> Modifier)?` immediately after `entry`. The surface receives a `Shape`, not `DialogSurfaceScope`; omitting `surface` selects the base overload in the official declaration. Both `entry: DropdownEntry` and `entries: List<DropdownEntry>` overloads exist; Window variants omit `renderInRootScaffold`. Check the specific overload instead of merging their parameter lists.

> [!TIP]
> To configure submenus for cascading menus, assign a child list to `DropdownItem.children`. The cascading engine recursively opens submenus on row click.
