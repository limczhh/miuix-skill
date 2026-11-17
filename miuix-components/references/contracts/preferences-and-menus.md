# Preference and Dropdown Menu Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: release tag `v0.9.5-beta01` at commit `6d2554a850b8960e2616c968263ff64beb5ffb03`.

Use when the task needs exact signatures, imports, defaults, data classes, or slot structures for `miuix-preference` rows, dropdowns, spinners, or anchored dropdown menus.

Before copying a call:
- Confirm the target project declares `top.yukonga.miuix.kmp:miuix-preference:0.9.5-beta01`.
- Verify the package imports: preference rows live under `top.yukonga.miuix.kmp.preference.*`, menu wrappers under `top.yukonga.miuix.kmp.menu.*`, and dropdown models under `top.yukonga.miuix.kmp.basic.*`.
- `Overlay*` components render through `MiuixPopupHost` in `Scaffold` (controlled by `renderInRootScaffold`). `Window*` components render at the window layer without requiring a `Scaffold` host.

---

## Standard Preference Rows

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.preference.ArrowPreference
import top.yukonga.miuix.kmp.preference.CheckboxLocation
import top.yukonga.miuix.kmp.preference.CheckboxPreference
import top.yukonga.miuix.kmp.preference.RadioButtonLocation
import top.yukonga.miuix.kmp.preference.RadioButtonPreference
import top.yukonga.miuix.kmp.preference.RadioButtonPreferenceColors
import top.yukonga.miuix.kmp.preference.RadioButtonPreferenceDefaults
import top.yukonga.miuix.kmp.preference.SwitchPreference
```

### SwitchPreference

```kotlin
@Composable
fun SwitchPreference(
    checked: Boolean,
    onCheckedChange: (Boolean) -> Unit,
    title: String,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    startAction: @Composable (() -> Unit)? = null,
    endActions: @Composable RowScope.() -> Unit = {},
    bottomAction: (@Composable () -> Unit)? = null,
    switchColors: SwitchColors = SwitchDefaults.switchColors(),
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    holdDownState: Boolean = false,
    enabled: Boolean = true,
)
```

### ArrowPreference

```kotlin
@Composable
fun ArrowPreference(
    title: String,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    startAction: @Composable (() -> Unit)? = null,
    endActions: @Composable RowScope.() -> Unit = {},
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    onClick: (() -> Unit)? = null,
    holdDownState: Boolean = false,
    enabled: Boolean = true,
)
```

### CheckboxPreference

```kotlin
@Composable
fun CheckboxPreference(
    title: String,
    checked: Boolean,
    onCheckedChange: ((Boolean) -> Unit)?,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    checkboxColors: CheckboxColors = CheckboxDefaults.checkboxColors(),
    startAction: @Composable (() -> Unit)? = null,
    endActions: @Composable (RowScope.() -> Unit)? = null,
    checkboxLocation: CheckboxLocation = CheckboxLocation.Start,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    holdDownState: Boolean = false,
    enabled: Boolean = true,
)
```

### RadioButtonPreference

```kotlin
@Composable
fun RadioButtonPreference(
    title: String,
    selected: Boolean,
    onClick: (() -> Unit)?,
    modifier: Modifier = Modifier,
    summary: String? = null,
    colors: RadioButtonPreferenceColors = RadioButtonPreferenceDefaults.radioButtonPreferenceColors(),
    radioButtonColors: RadioButtonColors = RadioButtonDefaults.radioButtonColors(),
    startAction: @Composable (() -> Unit)? = null,
    endActions: @Composable (RowScope.() -> Unit)? = null,
    radioButtonLocation: RadioButtonLocation = RadioButtonLocation.Start,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    holdDownState: Boolean = false,
    enabled: Boolean = true,
)
```
> [!NOTE]
> `RadioButtonPreference` uses `colors: RadioButtonPreferenceColors` instead of separate `titleColor`/`summaryColor` parameters.

---

## Slider Preferences

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/SliderPreference.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.preference.SliderPreference
import top.yukonga.miuix.kmp.preference.RangeSliderPreference
import top.yukonga.miuix.kmp.basic.SliderDefaults
```

### SliderPreference

```kotlin
@Composable
fun SliderPreference(
    value: Float,
    onValueChange: (Float) -> Unit,
    modifier: Modifier = Modifier,
    title: String? = null,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    startAction: @Composable (() -> Unit)? = null,
    valueText: String? = null,
    endActions: @Composable (RowScope.() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    onClick: (() -> Unit)? = null,
    holdDownState: Boolean = false,
    enabled: Boolean = true,
    valueRange: ClosedFloatingPointRange<Float> = 0f..1f,
    @IntRange(from = 0) steps: Int = 0,
    onValueChangeFinished: (() -> Unit)? = null,
    reverseDirection: Boolean = false,
    sliderHeight: Dp = SliderDefaults.MinHeight,
    sliderColors: SliderColors = SliderDefaults.sliderColors(),
    hapticEffect: SliderDefaults.SliderHapticEffect = SliderDefaults.DefaultHapticEffect,
    showKeyPoints: Boolean = false,
    keyPoints: List<Float>? = null,
    magnetThreshold: Float = 0.02f,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
)
```

### RangeSliderPreference

```kotlin
@Composable
fun RangeSliderPreference(
    value: ClosedFloatingPointRange<Float>,
    onValueChange: (ClosedFloatingPointRange<Float>) -> Unit,
    modifier: Modifier = Modifier,
    title: String? = null,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    startAction: @Composable (() -> Unit)? = null,
    valueText: String? = null,
    endActions: @Composable (RowScope.() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    onClick: (() -> Unit)? = null,
    holdDownState: Boolean = false,
    enabled: Boolean = true,
    valueRange: ClosedFloatingPointRange<Float> = 0f..1f,
    @IntRange(from = 0) steps: Int = 0,
    onValueChangeFinished: (() -> Unit)? = null,
    sliderHeight: Dp = SliderDefaults.MinHeight,
    sliderColors: SliderColors = SliderDefaults.sliderColors(),
    hapticEffect: SliderDefaults.SliderHapticEffect = SliderDefaults.DefaultHapticEffect,
    showKeyPoints: Boolean = false,
    keyPoints: List<Float>? = null,
    magnetThreshold: Float = 0.02f,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
)
```

---

## Dropdown & Spinner Preferences

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.preference.OverlayDropdownPreference
import top.yukonga.miuix.kmp.preference.WindowDropdownPreference
import top.yukonga.miuix.kmp.preference.OverlaySpinnerPreference
import top.yukonga.miuix.kmp.preference.WindowSpinnerPreference
import top.yukonga.miuix.kmp.basic.DropdownItem
import top.yukonga.miuix.kmp.basic.DropdownDefaults
```

### Dropdown Models

```kotlin
data class DropdownItem(
    val text: String,
    val enabled: Boolean = true,
    val selected: Boolean = false,
    val onClick: (() -> Unit)? = null,
    val icon: @Composable ((Modifier) -> Unit)? = null,
    val summary: String? = null,
    val children: List<DropdownItem>? = null,
)

data class DropdownEntry(
    val items: List<DropdownItem>,
    val enabled: Boolean = true,
)
```

### OverlayDropdownPreference & WindowDropdownPreference (String List)

```kotlin
@Composable
fun OverlayDropdownPreference(
    items: List<String>,
    selectedIndex: Int,
    title: String,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    dropdownColors: DropdownColors = DropdownDefaults.dropdownColors(),
    startAction: @Composable (() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    maxHeight: Dp? = null,
    enabled: Boolean = true,
    showValue: Boolean = true,
    renderInRootScaffold: Boolean = true,
    onExpandedChange: ((Boolean) -> Unit)? = null,
    onSelectedIndexChange: ((Int) -> Unit)? = null,
)

@Composable
fun WindowDropdownPreference(
    items: List<String>,
    selectedIndex: Int,
    title: String,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    dropdownColors: DropdownColors = DropdownDefaults.dropdownColors(),
    startAction: @Composable (() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    maxHeight: Dp? = null,
    enabled: Boolean = true,
    showValue: Boolean = true,
    onExpandedChange: ((Boolean) -> Unit)? = null,
    onSelectedIndexChange: ((Int) -> Unit)? = null,
)
```

### OverlaySpinnerPreference & WindowSpinnerPreference (Rich DropdownItem)

```kotlin
@Composable
fun OverlaySpinnerPreference(
    items: List<DropdownItem>,
    selectedIndex: Int,
    title: String,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    spinnerColors: DropdownColors = DropdownDefaults.dropdownColors(),
    startAction: @Composable (() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    maxHeight: Dp? = null,
    enabled: Boolean = true,
    showValue: Boolean = true,
    renderInRootScaffold: Boolean = true,
    onExpandedChange: ((Boolean) -> Unit)? = null,
    onSelectedIndexChange: ((Int) -> Unit)? = null,
    dialogMode: Boolean = false,
)

@Composable
fun WindowSpinnerPreference(
    items: List<DropdownItem>,
    selectedIndex: Int,
    title: String,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    spinnerColors: DropdownColors = DropdownDefaults.dropdownColors(),
    startAction: @Composable (() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    maxHeight: Dp? = null,
    enabled: Boolean = true,
    showValue: Boolean = true,
    onExpandedChange: ((Boolean) -> Unit)? = null,
    onSelectedIndexChange: ((Int) -> Unit)? = null,
)
```

---

## Anchored Dropdown Menus

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/menu/`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.menu.OverlayDropdownMenu
import top.yukonga.miuix.kmp.menu.WindowDropdownMenu
import top.yukonga.miuix.kmp.menu.OverlayIconDropdownMenu
import top.yukonga.miuix.kmp.menu.WindowIconDropdownMenu
import top.yukonga.miuix.kmp.menu.OverlayIconCascadingDropdownMenu
import top.yukonga.miuix.kmp.menu.WindowIconCascadingDropdownMenu
```

### OverlayDropdownMenu & WindowDropdownMenu (Row Anchor)

```kotlin
@Composable
fun OverlayDropdownMenu(
    entry: DropdownEntry,
    title: String,
    modifier: Modifier = Modifier,
    titleColor: BasicComponentColors = BasicComponentDefaults.titleColor(),
    summary: String? = null,
    summaryColor: BasicComponentColors = BasicComponentDefaults.summaryColor(),
    dropdownColors: DropdownColors = DropdownDefaults.dropdownColors(),
    startAction: @Composable (() -> Unit)? = null,
    bottomAction: (@Composable () -> Unit)? = null,
    insideMargin: PaddingValues = BasicComponentDefaults.InsideMargin,
    maxHeight: Dp? = null,
    enabled: Boolean = true,
    renderInRootScaffold: Boolean = true,
    collapseOnSelection: Boolean = true,
    onExpandedChange: ((Boolean) -> Unit)? = null,
)
```

### OverlayIconDropdownMenu & WindowIconDropdownMenu (IconButton Anchor)

```kotlin
@Composable
fun OverlayIconDropdownMenu(
    entry: DropdownEntry,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    maxHeight: Dp? = null,
    dropdownColors: DropdownColors = DropdownDefaults.dropdownColors(),
    renderInRootScaffold: Boolean = true,
    collapseOnSelection: Boolean = true,
    onExpandedChange: ((Boolean) -> Unit)? = null,
    backgroundColor: Color = Color.Unspecified,
    cornerRadius: Dp = IconButtonDefaults.CornerRadius,
    minHeight: Dp = IconButtonDefaults.MinHeight,
    minWidth: Dp = IconButtonDefaults.MinWidth,
    content: @Composable () -> Unit,
)
```

### OverlayIconCascadingDropdownMenu & WindowIconCascadingDropdownMenu (Submenu Cascading)

```kotlin
@Composable
fun OverlayIconCascadingDropdownMenu(
    entry: DropdownEntry,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    maxHeight: Dp? = null,
    dropdownColors: DropdownColors = DropdownDefaults.dropdownColors(),
    renderInRootScaffold: Boolean = true,
    collapseOnSelection: Boolean = true,
    surface: (@Composable DialogSurfaceScope.() -> Modifier)? = null,
    onExpandedChange: ((Boolean) -> Unit)? = null,
    backgroundColor: Color = Color.Unspecified,
    cornerRadius: Dp = IconButtonDefaults.CornerRadius,
    minHeight: Dp = IconButtonDefaults.MinHeight,
    minWidth: Dp = IconButtonDefaults.MinWidth,
    content: @Composable () -> Unit,
)
```
> [!TIP]
> To configure submenus for cascading menus, assign a child list to `DropdownItem.children`. The cascading engine recursively opens submenus on row click.
