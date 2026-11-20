# Input, Control, and Action Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta02 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

## Buttons & Action Bars

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Button.kt`, `IconButton.kt`, `FloatingActionButton.kt`, `FloatingToolbar.kt`

### Button & TextButton

Read the pinned documentation/source: [Button / TextButton](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/button.md).

### IconButton

Read the pinned documentation/source: [IconButton](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/iconbutton.md).

### FloatingActionButton & FloatingToolbar

Read the pinned documentation/source: [FloatingActionButton](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/floatingactionbutton.md), [FloatingToolbar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/floatingtoolbar.md).

---

## Text Field

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TextField.kt`

Read the pinned documentation/source: [TextField](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/textfield.md).
> [!WARNING]
> Miuix `TextField` has NO `isError` or `supportingText` parameter. Validation styling and error text must be composed externally.

The `TextField(state = ...)` overload is the beta02 source-of-truth. The `value: String` and `value: TextFieldValue` overloads remain available only as deprecated migration surfaces. For new code prefer `rememberTextFieldState`, `InputTransformation`, `OutputTransformation`, `TextFieldLineLimits`, and `onKeyboardAction`; do not copy a beta01 value-based recipe into a beta02 implementation without calling out the deprecation.

## Progress indicators

**Module:** `miuix-ui`
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/ProgressIndicator.kt`

Read the pinned documentation/source: [ProgressIndicator](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/progressindicator.md).

Beta02 adds `progress: () -> Float` overloads for both `LinearProgressIndicator` and `CircularProgressIndicator`. Use the lambda overload when progress changes every frame so the indicator reads it in the draw phase; use the `Float`/nullable overload for ordinary state updates and indeterminate circular progress.

---

## Basic Controls: Switch, Checkbox, RadioButton

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Switch.kt`, `Checkbox.kt`, `RadioButton.kt`

### Switch

Read the pinned documentation/source: [Switch](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/switch.md).

### Checkbox

Read the pinned documentation/source: [Checkbox](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/checkbox.md).
> [!IMPORTANT]
> The basic `Checkbox` takes `state: ToggleableState` and `onClick: (() -> Unit)?`, NOT a `checked: Boolean` or `onCheckedChange` lambda. For simple boolean toggles, pass `ToggleableState(checked)` and update your state in `onClick`. (In contrast, `CheckboxPreference` accepts `checked: Boolean`).

### RadioButton

Read the pinned documentation/source: [RadioButton](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/radiobutton.md).

---

## Sliders & Range Sliders

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Slider.kt`

### Slider

Read the pinned documentation/source: [Slider](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/slider.md).

### RangeSlider

Read the pinned documentation/source: [RangeSlider](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/slider.md).

---

## NumberPicker

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NumberPicker.kt`

Read the pinned documentation/source: [NumberPicker](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/numberpicker.md).
> [!NOTE]
> `visibleItemCount` must be odd and at least 3 (default is 5). `range` must not be empty.

---

## SearchBar & InputField

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/SearchBar.kt`

Read the pinned documentation/source: [SearchBar / InputField](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/basic/searchbar.md).

Minimal composition pattern:

```kotlin
import androidx.compose.runtime.saveable.rememberSaveable

var query by rememberSaveable { mutableStateOf("") }
var expanded by rememberSaveable { mutableStateOf(false) }

SearchBar(
    inputField = {
        InputField(
            query = query,
            onQueryChange = { query = it },
            onSearch = { /* Perform search */ },
            expanded = expanded,
            onExpandedChange = { expanded = it },
            label = "Search settings",
        )
    },
    expanded = expanded,
    onExpandedChange = { expanded = it },
) {
    // Search result list shown when expanded
}
```

The minimal shape saves query and stable expansion intent when the host supplies saveable state; it does not persist application settings or restore focus by itself. Reuse an existing screen owner when present. For restored focus/IME, cancel/back and result-state coordination, follow the [search rules](../../../miuix-guidelines/references/rules/settings-and-overlays.md#search).
