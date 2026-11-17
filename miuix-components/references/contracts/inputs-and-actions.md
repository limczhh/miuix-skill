# Input, Control, and Action Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: release tag `v0.9.5-beta01` at commit `6d2554a850b8960e2616c968263ff64beb5ffb03`.

Use when the task needs exact signatures, parameters, imports, or defaults for Miuix basic inputs, buttons, sliders, selection controls, search bars, or pickers.

> [!NOTE]
> For complete settings rows and dropdown/spinner menus (e.g. `SwitchPreference`, `CheckboxPreference`, `RadioButtonPreference`, `SliderPreference`, `OverlayDropdownPreference`, etc.), read [Preference and Dropdown Menu Contracts](preferences-and-menus.md).

Before copying a call:
- Check the target project's declared Miuix dependency (`top.yukonga.miuix.kmp:miuix-ui:0.9.5-beta01`).
- Treat the declaration, package/import, module, defaults, state/callback direction, and host requirement as one contract.
- An omitted parameter is not permission to invent an overload or transfer a Material parameter.

---

## Buttons & Action Bars

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Button.kt`, `IconButton.kt`, `FloatingActionButton.kt`, `FloatingToolbar.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.basic.Button
import top.yukonga.miuix.kmp.basic.ButtonDefaults
import top.yukonga.miuix.kmp.basic.FloatingActionButton
import top.yukonga.miuix.kmp.basic.FloatingActionButtonDefaults
import top.yukonga.miuix.kmp.basic.FloatingToolbar
import top.yukonga.miuix.kmp.basic.FloatingToolbarDefaults
import top.yukonga.miuix.kmp.basic.IconButton
import top.yukonga.miuix.kmp.basic.IconButtonDefaults
import top.yukonga.miuix.kmp.basic.TextButton
```

### Button & TextButton

```kotlin
@Composable
fun Button(
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    cornerRadius: Dp = ButtonDefaults.CornerRadius,
    minWidth: Dp = ButtonDefaults.MinWidth,
    minHeight: Dp = ButtonDefaults.MinHeight,
    colors: ButtonColors = ButtonDefaults.buttonColors(),
    insideMargin: PaddingValues = ButtonDefaults.InsideMargin,
    interactionSource: MutableInteractionSource? = null,
    indication: Indication? = LocalIndication.current,
    content: @Composable RowScope.() -> Unit,
)

@Composable
fun TextButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    cornerRadius: Dp = ButtonDefaults.CornerRadius,
    minWidth: Dp = ButtonDefaults.MinWidth,
    minHeight: Dp = ButtonDefaults.MinHeight,
    colors: TextButtonColors = ButtonDefaults.textButtonColors(),
    insideMargin: PaddingValues = ButtonDefaults.InsideMargin,
    textStyle: TextStyle? = null,
    interactionSource: MutableInteractionSource? = null,
    indication: Indication? = LocalIndication.current,
)
```

### IconButton

```kotlin
@Composable
fun IconButton(
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    holdDownState: Boolean = false,
    backgroundColor: Color = Color.Unspecified,
    cornerRadius: Dp = IconButtonDefaults.CornerRadius,
    minHeight: Dp = IconButtonDefaults.MinHeight,
    minWidth: Dp = IconButtonDefaults.MinWidth,
    content: @Composable () -> Unit,
)
```

### FloatingActionButton & FloatingToolbar

```kotlin
@Composable
fun FloatingActionButton(
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    shape: Shape = CircleShape,
    containerColor: Color = MiuixTheme.colorScheme.primary,
    shadowElevation: Dp = FloatingActionButtonDefaults.ShadowElevation,
    minWidth: Dp = FloatingActionButtonDefaults.MinWidth,
    minHeight: Dp = FloatingActionButtonDefaults.MinHeight,
    content: @Composable () -> Unit,
)

@Composable
fun FloatingToolbar(
    modifier: Modifier = Modifier,
    color: Color = FloatingToolbarDefaults.defaultColor(),
    cornerRadius: Dp = FloatingToolbarDefaults.CornerRadius,
    outSidePadding: PaddingValues = FloatingToolbarDefaults.OutSidePadding,
    shadowElevation: Dp = 4.dp,
    showDivider: Boolean = false,
    content: @Composable () -> Unit,
)
```

---

## Text Field

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TextField.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.basic.TextField
import top.yukonga.miuix.kmp.basic.TextFieldDefaults
```

```kotlin
@Composable
fun TextField(
    value: String,
    onValueChange: (String) -> Unit,
    modifier: Modifier = Modifier,
    insideMargin: DpSize = TextFieldDefaults.InsideMargin,
    colors: TextFieldColors = TextFieldDefaults.textFieldColors(),
    cornerRadius: Dp = TextFieldDefaults.CornerRadius,
    label: String = "",
    useLabelAsPlaceholder: Boolean = false,
    enabled: Boolean = true,
    readOnly: Boolean = false,
    textStyle: TextStyle = MiuixTheme.textStyles.main,
    keyboardOptions: KeyboardOptions = KeyboardOptions.Default,
    keyboardActions: KeyboardActions = KeyboardActions.Default,
    leadingIcon: @Composable (() -> Unit)? = null,
    trailingIcon: @Composable (() -> Unit)? = null,
    singleLine: Boolean = false,
    maxLines: Int = if (singleLine) 1 else Int.MAX_VALUE,
    minLines: Int = 1,
    visualTransformation: VisualTransformation = VisualTransformation.None,
    onTextLayout: (TextLayoutResult) -> Unit = {},
    interactionSource: MutableInteractionSource? = null,
    cursorBrush: Brush = SolidColor(colors.borderColor),
)
```
> [!WARNING]
> Miuix `TextField` has NO `isError` or `supportingText` parameter. Validation styling and error text must be composed externally.

---

## Basic Controls: Switch, Checkbox, RadioButton

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Switch.kt`, `Checkbox.kt`, `RadioButton.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.basic.Switch
import top.yukonga.miuix.kmp.basic.SwitchDefaults
import top.yukonga.miuix.kmp.basic.Checkbox
import top.yukonga.miuix.kmp.basic.CheckboxDefaults
import top.yukonga.miuix.kmp.basic.RadioButton
import top.yukonga.miuix.kmp.basic.RadioButtonDefaults
import androidx.compose.ui.state.ToggleableState
```

### Switch

```kotlin
@Composable
fun Switch(
    checked: Boolean,
    onCheckedChange: ((Boolean) -> Unit)?,
    modifier: Modifier = Modifier,
    colors: SwitchColors = SwitchDefaults.switchColors(),
    enabled: Boolean = true,
)
```

### Checkbox

```kotlin
@Composable
fun Checkbox(
    state: ToggleableState,
    onClick: (() -> Unit)?,
    modifier: Modifier = Modifier,
    colors: CheckboxColors = CheckboxDefaults.checkboxColors(),
    enabled: Boolean = true,
)
```
> [!IMPORTANT]
> The basic `Checkbox` takes `state: ToggleableState` and `onClick: (() -> Unit)?`, NOT a `checked: Boolean` or `onCheckedChange` lambda. For simple boolean toggles, pass `ToggleableState(checked)` and update your state in `onClick`. (In contrast, `CheckboxPreference` accepts `checked: Boolean`).

### RadioButton

```kotlin
@Composable
fun RadioButton(
    selected: Boolean,
    onClick: (() -> Unit)?,
    modifier: Modifier = Modifier,
    colors: RadioButtonColors = RadioButtonDefaults.radioButtonColors(),
    enabled: Boolean = true,
)
```

---

## Sliders & Range Sliders

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Slider.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.basic.Slider
import top.yukonga.miuix.kmp.basic.RangeSlider
import top.yukonga.miuix.kmp.basic.SliderDefaults
```

### Slider

```kotlin
@Composable
fun Slider(
    value: Float,
    onValueChange: (Float) -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    valueRange: ClosedFloatingPointRange<Float> = 0f..1f,
    @IntRange(from = 0) steps: Int = 0,
    onValueChangeFinished: (() -> Unit)? = null,
    reverseDirection: Boolean = false,
    height: Dp = SliderDefaults.MinHeight,
    colors: SliderColors = SliderDefaults.sliderColors(),
    hapticEffect: SliderDefaults.SliderHapticEffect = SliderDefaults.DefaultHapticEffect,
    showKeyPoints: Boolean = false,
    keyPoints: List<Float>? = null,
    magnetThreshold: Float = 0.02f,
)
```

### RangeSlider

```kotlin
@Composable
fun RangeSlider(
    value: ClosedFloatingPointRange<Float>,
    onValueChange: (ClosedFloatingPointRange<Float>) -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    valueRange: ClosedFloatingPointRange<Float> = 0f..1f,
    @IntRange(from = 0) steps: Int = 0,
    onValueChangeFinished: (() -> Unit)? = null,
    height: Dp = SliderDefaults.MinHeight,
    colors: SliderColors = SliderDefaults.sliderColors(),
    hapticEffect: SliderDefaults.SliderHapticEffect = SliderDefaults.DefaultHapticEffect,
    showKeyPoints: Boolean = false,
    keyPoints: List<Float>? = null,
    magnetThreshold: Float = 0.02f,
)
```

---

## NumberPicker

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NumberPicker.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.basic.NumberPicker
import top.yukonga.miuix.kmp.basic.NumberPickerDefaults
```

```kotlin
@Composable
fun NumberPicker(
    value: Int,
    onValueChange: (Int) -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    range: IntRange = 0..10,
    label: (Int) -> String = { it.toString() },
    visibleItemCount: Int = 5,
    wrapAround: Boolean = false,
    colors: NumberPickerColors = NumberPickerDefaults.colors(),
    textStyle: TextStyle = MiuixTheme.textStyles.title1,
    itemHeight: Dp = NumberPickerDefaults.ItemHeight,
)
```
> [!NOTE]
> `visibleItemCount` must be odd and at least 3 (default is 5). `range` must not be empty.

---

## SearchBar & InputField

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/SearchBar.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.basic.SearchBar
import top.yukonga.miuix.kmp.basic.SearchBarDefaults
import top.yukonga.miuix.kmp.basic.InputField
```

```kotlin
@Composable
fun SearchBar(
    inputField: @Composable () -> Unit,
    onExpandedChange: (Boolean) -> Unit,
    modifier: Modifier = Modifier,
    insideMargin: DpSize = SearchBarDefaults.InsideMargin,
    expanded: Boolean = false,
    outsideEndAction: @Composable (() -> Unit)? = null,
    content: @Composable ColumnScope.() -> Unit,
)

@Composable
fun InputField(
    query: String,
    onQueryChange: (String) -> Unit,
    onSearch: (String) -> Unit,
    expanded: Boolean,
    onExpandedChange: (Boolean) -> Unit,
    modifier: Modifier = Modifier,
    label: String = "",
    enabled: Boolean = true,
    textStyle: TextStyle? = null,
    color: Color = MiuixTheme.colorScheme.surfaceContainerHigh,
    leadingIcon: @Composable (() -> Unit)? = null,
    trailingIcon: @Composable (() -> Unit)? = null,
    interactionSource: MutableInteractionSource? = null,
)
```

Minimal composition pattern:

```kotlin
var query by remember { mutableStateOf("") }
var expanded by remember { mutableStateOf(false) }

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
