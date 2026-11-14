# Verified API Contracts

This is a curated, source-checked fast path for Miuix `v0.9.4` at commit `39c40f99844227b853f0049a0933b1f3ae6c00ba`. It answers the narrow question “what is the real public call shape?” for the APIs that most often define a page or cause compile errors.

It is not a complete component catalog and it is not a page template. The page skeleton remains independent from the component choice. When an API is not covered here, use the [API verification gate](source-verification.md#api-verification-gate); never fill the gap by analogy with Material, Material 3, or another Miuix overload.

Read only the section matching the symbol being used. Do not load this entire sheet for a page that uses one unrelated component.

## Contract rules

- Every contract below is checked against the pinned `commonMain` source. The source path is part of the contract.
- The declaration block records the public parameter names, order, types, and defaults. The minimal call uses named arguments so it is safer to adapt, but it is not a self-contained target-project file.
- The Miuix import block identifies the source package for the covered symbols. Standard Compose imports and the target project's dependency wiring still require checking in the target project.
- An omitted optional parameter is not permission to invent a parameter or overload. Read the declaration before adding one.
- `source-verified` means the declaration and behavior were checked in the pinned source. It does not mean the target project compiled it. Report `compile-verified` only after compiling the affected target.
- On a Miuix version change, treat every block as stale until its source path and declaration are checked again.

Contents: [theme](#theme) · [page host](#page-host) · [top app bar](#top-app-bar) · [collapsing top bar behavior](#collapsing-top-bar-behavior) · [card and section title](#card-and-section-title) · [navigation bar](#navigation-bar) · [floating navigation bar](#floating-navigation-bar) · [dialogs](#dialogs) · [preferences](#preferences) · [text field](#text-field) · [buttons](#buttons) · [uncatalogued APIs](#when-an-api-is-not-in-this-sheet)

## Theme

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/MiuixTheme.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/ThemeController.kt`  
**Documentation:** `docs/guide/theme.md`

The controller overload is the normal application-root call. `ThemeController` is remembered by the root owner; it is not recreated by each page.

```kotlin
@Composable
fun MiuixTheme(
    controller: ThemeController,
    textStyles: TextStyles = MiuixTheme.textStyles,
    content: @Composable () -> Unit,
)

@Composable
fun MiuixTheme(
    colors: Colors = MiuixTheme.colorScheme,
    textStyles: TextStyles = MiuixTheme.textStyles,
    content: @Composable () -> Unit,
)

@Stable
class ThemeController(
    colorSchemeMode: ColorSchemeMode = ColorSchemeMode.System,
    lightColors: Colors = lightColorScheme(),
    darkColors: Colors = darkColorScheme(),
    keyColor: Color? = null,
    colorSpec: ThemeColorSpec = ThemeColorSpec.Spec2021,
    paletteStyle: ThemePaletteStyle = ThemePaletteStyle.TonalSpot,
    isDark: Boolean? = null,
)
```

Minimal root call:

```kotlin
import androidx.compose.runtime.Composable
import androidx.compose.runtime.remember
import top.yukonga.miuix.kmp.theme.ColorSchemeMode
import top.yukonga.miuix.kmp.theme.MiuixTheme
import top.yukonga.miuix.kmp.theme.ThemeController

@Composable
fun App() {
    val controller = remember { ThemeController(ColorSchemeMode.System) }

    MiuixTheme(controller = controller) {
        // Application shell and pages.
    }
}
```

Do not add a second page-level `MiuixTheme` only to obtain colors or text styles.

## Page host

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Scaffold.kt`  
**Documentation:** `docs/components/scaffold.md`  
**Demo:** `docs/demo/src/commonMain/kotlin/ScaffoldDemo.kt`

Miuix import:

```kotlin
import top.yukonga.miuix.kmp.basic.Scaffold
```

Exact public declaration:

```kotlin
@Composable
fun Scaffold(
    modifier: Modifier = Modifier,
    topBar: @Composable () -> Unit = {},
    bottomBar: @Composable () -> Unit = {},
    floatingActionButton: @Composable () -> Unit = {},
    floatingActionButtonPosition: FabPosition = FabPosition.End,
    floatingToolbar: @Composable () -> Unit = {},
    floatingToolbarPosition: ToolbarPosition = ToolbarPosition.BottomCenter,
    snackbarHost: @Composable () -> Unit = {},
    popupHost: @Composable () -> Unit = { MiuixPopupHost() },
    containerColor: Color = MiuixTheme.colorScheme.surface,
    contentWindowInsets: WindowInsets = WindowInsets.systemBars.union(WindowInsets.displayCutout),
    content: @Composable (PaddingValues) -> Unit,
)
```

Minimal content call:

```kotlin
Scaffold(
    topBar = { TopAppBar(title = "Page") },
) { innerPadding ->
    Column(
        modifier = Modifier
            .padding(innerPadding)
            .consumeWindowInsets(innerPadding),
    ) {
        // Primary page content.
    }
}
```

The `content` lambda receives `PaddingValues`; apply and consume them at one known content boundary. For a `LazyColumn`, use the same boundary for its `contentPadding`/inset consumption. If using `Modifier.verticalScroll`, apply the padding to the scroll child rather than to the scroll modifier itself. A top or bottom bar may already own its own default window-inset padding; do not add the same system inset again to every child.

## Top app bar

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`  
**Documentation:** `docs/components/topappbar.md`  
**Demo:** `docs/demo/src/commonMain/kotlin/TopAppBarDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.MiuixScrollBehavior
import top.yukonga.miuix.kmp.basic.SmallTopAppBar
import top.yukonga.miuix.kmp.basic.TopAppBar
import top.yukonga.miuix.kmp.basic.rememberTopAppBarState
```

`TopAppBar` and `SmallTopAppBar` are separate public functions. The title is a `String`, the actions use `RowScope`, and `scrollBehavior` is nullable.

```kotlin
@Composable
fun TopAppBar(
    title: String,
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surface,
    titleColor: Color = MiuixTheme.colorScheme.onSurface,
    largeTitle: String = title,
    largeTitleColor: Color = MiuixTheme.colorScheme.onSurface,
    subtitle: String = "",
    subtitleColor: Color = MiuixTheme.colorScheme.onSurfaceVariantSummary,
    navigationIcon: @Composable () -> Unit = {},
    actions: @Composable RowScope.() -> Unit = {},
    scrollBehavior: ScrollBehavior? = null,
    defaultWindowInsetsPadding: Boolean = true,
    titlePadding: Dp = TopAppBarDefaults.TitlePadding,
    navigationIconPadding: Dp = TopAppBarDefaults.NavigationIconPadding,
    actionIconPadding: Dp = TopAppBarDefaults.ActionIconPadding,
    bottomContent: @Composable () -> Unit = {},
)

@Composable
fun SmallTopAppBar(
    title: String,
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surface,
    titleColor: Color = MiuixTheme.colorScheme.onSurface,
    subtitle: String = "",
    subtitleColor: Color = MiuixTheme.colorScheme.onSurfaceVariantSummary,
    navigationIcon: @Composable () -> Unit = {},
    actions: @Composable RowScope.() -> Unit = {},
    scrollBehavior: ScrollBehavior? = null,
    defaultWindowInsetsPadding: Boolean = true,
    titlePadding: Dp = TopAppBarDefaults.TitlePadding,
    navigationIconPadding: Dp = TopAppBarDefaults.NavigationIconPadding,
    actionIconPadding: Dp = TopAppBarDefaults.ActionIconPadding,
    bottomContent: @Composable () -> Unit = {},
)
```

Minimal call:

```kotlin
TopAppBar(
    title = "Page",
    subtitle = "Optional summary",
)
```

Use `SmallTopAppBar` when the bar must remain pinned. Do not pass a composable title or invent a separate `largeTitle` parameter for `SmallTopAppBar`.

## Collapsing top bar behavior

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt`  
**Documentation:** `docs/components/topappbar.md`, `docs/components/scaffold.md`  
**Demo:** `docs/demo/src/commonMain/kotlin/TopAppBarDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.MiuixScrollBehavior
import top.yukonga.miuix.kmp.basic.Scaffold
import top.yukonga.miuix.kmp.basic.TopAppBar
import top.yukonga.miuix.kmp.basic.rememberTopAppBarState
```

`MiuixScrollBehavior` returns the `ScrollBehavior` consumed by `TopAppBar` or `SmallTopAppBar`. The scrolling content must attach the same behavior's `nestedScrollConnection`; creating the behavior without attaching that connection does not make the bar collapse.

```kotlin
@Composable
fun MiuixScrollBehavior(
    state: TopAppBarState = rememberTopAppBarState(),
    canScroll: () -> Boolean = { true },
    snapAnimationSpec: AnimationSpec<Float>? = spring(stiffness = 2500f),
    flingAnimationSpec: DecayAnimationSpec<Float>? = rememberSplineBasedDecay(),
): ScrollBehavior

@Composable
fun rememberTopAppBarState(
    initialHeightOffsetLimit: Float = -Float.MAX_VALUE,
    initialHeightOffset: Float = 0f,
    initialContentOffset: Float = 0f,
): TopAppBarState
```

Minimal integration shape:

```kotlin
val scrollBehavior = MiuixScrollBehavior()

Scaffold(
    topBar = {
        TopAppBar(
            title = "Page",
            largeTitle = "Large page title",
            scrollBehavior = scrollBehavior,
        )
    },
) { innerPadding ->
    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .nestedScroll(scrollBehavior.nestedScrollConnection)
            .consumeWindowInsets(innerPadding),
        contentPadding = innerPadding,
    ) {
        // Page content.
    }
}
```

Use `TopAppBar` for the large-title/collapsing form. `SmallTopAppBar` is the pinned form; its source pins the shared scroll state rather than providing a second collapsing recipe. Keep the `LazyColumn`/scroll owner separate from the app bar state, and do not attach a different `nestedScrollConnection`.

## Card and section title

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Card.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/SmallTitle.kt`  
**Documentation:** `docs/components/card.md`, `docs/components/smalltitle.md`  
**Demo:** `docs/demo/src/commonMain/kotlin/CardDemo.kt`, `SmallTitleDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.Card
import top.yukonga.miuix.kmp.basic.SmallTitle
import top.yukonga.miuix.kmp.utils.PressFeedbackType
```

`Card` has a non-clickable overload and a clickable/long-press overload. The two overloads share the first parameters; use named arguments when selecting the interactive form.

```kotlin
@Composable
fun Card(
    modifier: Modifier = Modifier,
    cornerRadius: Dp = CardDefaults.CornerRadius,
    insideMargin: PaddingValues = CardDefaults.InsideMargin,
    colors: CardColors = CardDefaults.defaultColors(),
    content: @Composable ColumnScope.() -> Unit,
)

@Composable
fun Card(
    modifier: Modifier = Modifier,
    cornerRadius: Dp = CardDefaults.CornerRadius,
    insideMargin: PaddingValues = CardDefaults.InsideMargin,
    colors: CardColors = CardDefaults.defaultColors(),
    pressFeedbackType: PressFeedbackType = PressFeedbackType.None,
    showIndication: Boolean = false,
    holdDownState: Boolean = false,
    onClick: (() -> Unit)? = null,
    onLongPress: (() -> Unit)? = null,
    content: @Composable ColumnScope.() -> Unit,
)

@Composable
fun SmallTitle(
    text: String,
    modifier: Modifier = Modifier,
    textColor: Color = MiuixTheme.colorScheme.onBackgroundVariant,
    insideMargin: PaddingValues = SmallTitleDefaults.InsideMargin,
)
```

Pinned defaults that are safe to quote for this version:

- `CardDefaults.CornerRadius`: `16.dp`
- `CardDefaults.InsideMargin`: `PaddingValues(0.dp)`
- `SmallTitleDefaults.InsideMargin`: `PaddingValues(28.dp, 8.dp)`

Minimal grouping call:

```kotlin
SmallTitle(text = "Group")
Card {
    // Related content.
}
```

These defaults do not make `Card` mandatory for every page. Select it only when the content has a real containment relationship.

## Navigation bar

**Module:** `miuix-ui` (icons may additionally require `miuix-icons`)  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt`  
**Documentation:** `docs/components/navigationbar.md`  
**Demo:** `docs/demo/src/commonMain/kotlin/NavigationBarDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.NavigationBar
import top.yukonga.miuix.kmp.basic.NavigationBarDefaults
import top.yukonga.miuix.kmp.basic.NavigationBarDisplayMode
import top.yukonga.miuix.kmp.basic.NavigationBarItem
```

The source documents `NavigationBar` for 2–5 items. `NavigationBarItem` is a `RowScope` extension, so it must be called inside the bar content lambda.

```kotlin
@Composable
fun NavigationBar(
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surface,
    showDivider: Boolean = true,
    defaultWindowInsetsPadding: Boolean = true,
    mode: NavigationBarDisplayMode = NavigationBarDisplayMode.IconAndText,
    content: @Composable RowScope.() -> Unit,
)

@Composable
fun RowScope.NavigationBarItem(
    selected: Boolean,
    onClick: () -> Unit,
    icon: ImageVector,
    label: String,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    colors: NavigationBarItemColors = NavigationBarDefaults.navigationBarItemColors(),
    badge: (@Composable () -> Unit)? = null,
)
```

Minimal call shape:

```kotlin
NavigationBar {
    NavigationBarItem(
        selected = selectedIndex == 0,
        onClick = { selectedIndex = 0 },
        icon = itemIcon,
        label = "Home",
    )
}
```

`itemIcon` must be an actual `ImageVector`; do not replace it with a Material icon unless that dependency and icon choice are intentional. The `colors` parameter appears before `badge`; name `badge` instead of relying on positional arguments. The navigation bar presents the route owner's state; it must not create a second back stack.

## Floating navigation bar

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt`  
**Documentation:** `docs/components/navigationbar.md`  
**Demo:** `docs/demo/src/commonMain/kotlin/NavigationBarDemo.kt`

`FloatingNavigationBar` is still bottom navigation, not a generic overlay. Put it in the owning `Scaffold`'s `bottomBar` slot so the page content receives the correct bottom `PaddingValues`. The documented navigation pattern supports two to five icon items. The component always creates its own floating bottom spacing; `defaultWindowInsetsPadding = true` additionally applies the animated caption-bar inset. It does not mean that every surrounding bottom inset is transferred to the caller.

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.basic.FloatingNavigationBar
import top.yukonga.miuix.kmp.basic.FloatingNavigationBarDefaults
import top.yukonga.miuix.kmp.basic.FloatingNavigationBarItem
import top.yukonga.miuix.kmp.basic.NavigationBarDefaults
import top.yukonga.miuix.kmp.basic.NavigationBarItemColors
import top.yukonga.miuix.kmp.basic.Scaffold
```

```kotlin
@Composable
fun FloatingNavigationBar(
    modifier: Modifier = Modifier,
    color: Color = MiuixTheme.colorScheme.surfaceContainer,
    cornerRadius: Dp = FloatingToolbarDefaults.CornerRadius,
    horizontalAlignment: Alignment.Horizontal = CenterHorizontally,
    horizontalOutSidePadding: Dp = FloatingNavigationBarDefaults.HorizontalOutSidePadding,
    shadowElevation: Dp = FloatingNavigationBarDefaults.ShadowElevation,
    showDivider: Boolean = false,
    defaultWindowInsetsPadding: Boolean = true,
    content: @Composable () -> Unit,
)

@Composable
fun FloatingNavigationBarItem(
    selected: Boolean,
    onClick: () -> Unit,
    icon: ImageVector,
    label: String,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    colors: NavigationBarItemColors = NavigationBarDefaults.navigationBarItemColors(),
    badge: (@Composable () -> Unit)? = null,
)
```

Minimal host shape:

```kotlin
Scaffold(
    bottomBar = {
        FloatingNavigationBar {
            destinations.forEachIndexed { index, destination ->
                FloatingNavigationBarItem(
                    selected = selectedIndex == index,
                    onClick = { onDestinationClick(index) },
                    icon = destination.icon,
                    label = destination.label,
                )
            }
        }
    },
) { innerPadding ->
    // Apply innerPadding once at the primary content boundary.
}
```

The public parameter spelling is `horizontalOutSidePadding` with a capital `S`; do not silently change it to `horizontalOutsidePadding`. Its outside padding is applied by the source when the alignment is `Alignment.Start` or `Alignment.End`; centered content does not use that side padding. If `defaultWindowInsetsPadding` is disabled, the caller owns the caption-bar inset contract, but the component's own floating bottom spacing remains. Do not manually overlay the bar on a scrolling list unless that overlap is an intentional product decision.

## Dialogs

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayDialog.kt`, `window/WindowDialog.kt`  
**Documentation:** `docs/components/overlaydialog.md`, `docs/components/windowdialog.md`  
**Demo:** `docs/demo/src/commonMain/kotlin/OverlayDialogDemo.kt`, `WindowDialogDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.overlay.OverlayDialog
import top.yukonga.miuix.kmp.window.WindowDialog
```

The two functions have nearly the same content contract. The important difference is the host: `OverlayDialog` renders through the page/root `Scaffold` popup host; `WindowDialog` creates an independent platform window.

```kotlin
@Composable
fun OverlayDialog(
    show: Boolean,
    modifier: Modifier = Modifier,
    title: String? = null,
    titleColor: Color = DialogDefaults.titleColor(),
    summary: String? = null,
    summaryColor: Color = DialogDefaults.summaryColor(),
    backgroundColor: Color = DialogDefaults.backgroundColor(),
    enableWindowDim: Boolean = true,
    onDismissRequest: (() -> Unit)? = null,
    onDismissFinished: (() -> Unit)? = null,
    outsideMargin: DpSize = DialogDefaults.outsideMargin,
    insideMargin: DpSize = DialogDefaults.insideMargin,
    defaultWindowInsetsPadding: Boolean = true,
    renderInRootScaffold: Boolean = true,
    maxWidth: Dp = DialogDefaults.MaxWidth,
    largeScreen: Boolean? = null,
    cornerRadius: Dp? = null,
    content: @Composable () -> Unit,
)

@Composable
fun WindowDialog(
    show: Boolean,
    modifier: Modifier = Modifier,
    title: String? = null,
    titleColor: Color = DialogDefaults.titleColor(),
    summary: String? = null,
    summaryColor: Color = DialogDefaults.summaryColor(),
    backgroundColor: Color = DialogDefaults.backgroundColor(),
    enableWindowDim: Boolean = true,
    onDismissRequest: (() -> Unit)? = null,
    onDismissFinished: (() -> Unit)? = null,
    outsideMargin: DpSize = DialogDefaults.outsideMargin,
    insideMargin: DpSize = DialogDefaults.insideMargin,
    defaultWindowInsetsPadding: Boolean = true,
    maxWidth: Dp = DialogDefaults.MaxWidth,
    largeScreen: Boolean? = null,
    cornerRadius: Dp? = null,
    content: @Composable () -> Unit,
)
```

Minimal state and call shapes:

```kotlin
var showDialog by remember { mutableStateOf(false) }

OverlayDialog(
    show = showDialog,
    onDismissRequest = { showDialog = false },
) {
    // Dialog content.
}
```

Use `OverlayDialog` for a surface owned by the current page/shell. Use `WindowDialog` when the surface must be independent of the page `Scaffold`. In both cases, `show` and the dismissal callback are two sides of the same state owner; do not rely on an implicit close behavior.

## Preferences

**Module:** `miuix-preference`  
**Source:** `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/SwitchPreference.kt`, `ArrowPreference.kt`  
**Documentation:** `docs/components/switchpreference.md`, `docs/components/arrowpreference.md`  
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
**Documentation:** `docs/components/textfield.md`  
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
**Documentation:** `docs/components/button.md`  
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

## When an API is not in this sheet

Do not enlarge this file for every component. For a named API that is not covered:

1. Read the matching Miuix documentation for intent and the matching demo for an isolated call.
2. Inspect the declaration in the pinned source, including overload, imports/module, defaults, state, callbacks, and host requirements.
3. Compile the affected target when available and report the result separately from source inspection.
4. Add a new contract card here only when the API is high-frequency, structurally important, or has already caused repeated integration errors.

For navigation stack APIs, use the dedicated [miuix-nav guide](miuix-nav.md) and still pass the same verification gate; do not reconstruct the route contract from this page or from Example code.
