# Preference, Input, and Button Contracts

This is a focused, source-verified API contract guide for the maintained Miuix snapshot: upstream main at commit d8efe1872c08c863a8b33e7044dd2a8b43038032 (v0.9.4-80-gd8efe187, not a release tag).

Use when the task needs SwitchPreference, ArrowPreference, TextField, Button, or TextButton signatures.

Do not use when the task only needs grouping, form hierarchy, or validation presentation.

Before copying a call:

- Check the target project's resolved Miuix version, source set, and dependency substitution.
- Treat the declaration, package/import, module, defaults, state/callback direction, and host requirement as one contract.
- An omitted parameter is not permission to invent an overload or transfer a Material parameter.
- source-verified means the pinned declaration was checked; compile-verified requires compiling the affected target.

The exact source path is part of each contract below. If the target version differs, re-run the source-verification gate before presenting version-sensitive code.
## Preferences

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/SwitchPreference.kt`, `ArrowPreference.kt`  
**Documentation:** `docs/components/extended/switchpreference.md`, `docs/components/extended/arrowpreference.md`
**Demo:** `docs/demo/src/commonMain/kotlin/SwitchPreferenceDemo.kt`, `ArrowPreferenceDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.preference.ArrowPreference
import top.yukonga.miuix.kmp.preference.SwitchPreference
```

These are complete row components, not generic layout primitives. Their state still belongs to the screen owner or model.

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

Minimal calls:

```kotlin
SwitchPreference(
    checked = enabled,
    onCheckedChange = { enabled = it },
    title = "Enabled",
    summary = "Use the feature",
)

ArrowPreference(
    title = "More options",
    summary = "Opens another page or selector",
    onClick = onOpenMoreOptions,
)
```

`SwitchPreference` makes the row and switch drive the same `onCheckedChange` path. `ArrowPreference` can render with a nullable `onClick`, but a row intended to navigate or open a selector should receive the explicit callback.

## Text field

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TextField.kt`  
**Documentation:** `docs/components/basic/textfield.md`
**Demo:** `docs/demo/src/commonMain/kotlin/TextFieldDemo.kt`

Miuix import:

```kotlin
import top.yukonga.miuix.kmp.basic.TextField
```

For ordinary string state, use this overload. The same function also has `TextFieldValue` and `TextFieldState` overloads with different first parameters; do not mix their contracts.

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

Minimal call:

```kotlin
var value by remember { mutableStateOf("") }

TextField(
    value = value,
    onValueChange = { value = it },
    label = "Name",
    singleLine = true,
)
```

At this snapshot, this API has no `isError` or `supportingText` parameter. Put validation or supporting feedback in the surrounding page composition instead of inventing Material parameters.

## Buttons

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Button.kt`  
**Documentation:** `docs/components/basic/button.md`
**Demo:** `docs/demo/src/commonMain/kotlin/ButtonDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.Button
import top.yukonga.miuix.kmp.basic.TextButton
```

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

Minimal calls:

```kotlin
Button(onClick = onSave) {
    // RowScope content, commonly Miuix Text or an icon plus text.
}

TextButton(
    text = "Cancel",
    onClick = onCancel,
)
```

`Button` does not have a `text` parameter; `TextButton` does. Do not transfer parameters between the two overload families without checking the declaration.
