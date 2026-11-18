# Overlay, Window, Sheet, and Popup Contracts

This is a focused, catalog-verified API contract guide for the maintained Miuix snapshot: release tag `v0.9.5-beta01` at commit `6d2554a850b8960e2616c968263ff64beb5ffb03`.

Use when the task needs exact signatures, imports, defaults, dismissal callbacks, or host requirements for `OverlayDialog`, `WindowDialog`, `OverlayBottomSheet`, `WindowBottomSheet`, `OverlayListPopup`, `WindowListPopup`, or `Snackbar`.

Before copying a call:
- Check the target project's declared Miuix dependency (`top.yukonga.miuix.kmp:miuix-ui:0.9.5-beta01`).
- `Overlay*` components render through `MiuixPopupHost` (typically inside `Scaffold`). `renderInRootScaffold` determines whether the popup anchors to the outermost root Scaffold (full-screen) or current Scaffold.
- `Window*` components render in an independent platform window/popup layer, bypassing `Scaffold`.

---

Read only the matching section; these declaration blocks describe library APIs, not application reimplementations.

Jump to: [Dialogs](#dialogs) · [Bottom Sheets](#bottom-sheets) · [List Popups](#list-popups) · [Snackbars](#snackbars)

## Dialogs

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayDialog.kt`, `window/WindowDialog.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.overlay.OverlayDialog
import top.yukonga.miuix.kmp.window.WindowDialog
import top.yukonga.miuix.kmp.layout.DialogSurfaceScope
import top.yukonga.miuix.kmp.basic.DialogDefaults
```

### OverlayDialog & WindowDialog

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
    dimColor: Color = MiuixTheme.colorScheme.windowDimming,
    surface: (@Composable DialogSurfaceScope.() -> Modifier)? = null,
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

Minimal call:
```kotlin
var showDialog by remember { mutableStateOf(false) }

OverlayDialog(
    show = showDialog,
    title = "Notice",
    summary = "Operation cannot be undone",
    onDismissRequest = { showDialog = false },
) {
    // Dialog action buttons / content
}
```

---

## Bottom Sheets

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayBottomSheet.kt`, `window/WindowBottomSheet.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.overlay.OverlayBottomSheet
import top.yukonga.miuix.kmp.window.WindowBottomSheet
import top.yukonga.miuix.kmp.basic.BottomSheetDefaults
import top.yukonga.miuix.kmp.layout.BottomSheetSurfaceScope
```

### OverlayBottomSheet & WindowBottomSheet

```kotlin
@Composable
fun OverlayBottomSheet(
    show: Boolean,
    modifier: Modifier = Modifier,
    title: String? = null,
    startAction: @Composable (() -> Unit)? = null,
    endAction: @Composable (() -> Unit)? = null,
    backgroundColor: Color = BottomSheetDefaults.backgroundColor(),
    enableWindowDim: Boolean = true,
    cornerRadius: Dp = BottomSheetDefaults.cornerRadius,
    sheetMaxWidth: Dp = BottomSheetDefaults.maxWidth,
    onDismissRequest: (() -> Unit)? = null,
    onDismissFinished: (() -> Unit)? = null,
    outsideMargin: DpSize = BottomSheetDefaults.outsideMargin,
    insideMargin: DpSize = BottomSheetDefaults.insideMargin,
    defaultWindowInsetsPadding: Boolean = true,
    dragHandleColor: Color = BottomSheetDefaults.dragHandleColor(),
    allowDismiss: Boolean = true,
    enableNestedScroll: Boolean = true,
    renderInRootScaffold: Boolean = true,
    dimColor: Color = MiuixTheme.colorScheme.windowDimming,
    surface: (@Composable BottomSheetSurfaceScope.() -> Modifier)? = null,
    content: @Composable () -> Unit,
)

@Composable
fun WindowBottomSheet(
    show: Boolean,
    modifier: Modifier = Modifier,
    title: String? = null,
    startAction: @Composable (() -> Unit)? = null,
    endAction: @Composable (() -> Unit)? = null,
    backgroundColor: Color = BottomSheetDefaults.backgroundColor(),
    enableWindowDim: Boolean = true,
    cornerRadius: Dp = BottomSheetDefaults.cornerRadius,
    sheetMaxWidth: Dp = BottomSheetDefaults.maxWidth,
    onDismissRequest: (() -> Unit)? = null,
    onDismissFinished: (() -> Unit)? = null,
    outsideMargin: DpSize = BottomSheetDefaults.outsideMargin,
    insideMargin: DpSize = BottomSheetDefaults.insideMargin,
    defaultWindowInsetsPadding: Boolean = true,
    dragHandleColor: Color = BottomSheetDefaults.dragHandleColor(),
    allowDismiss: Boolean = true,
    enableNestedScroll: Boolean = true,
    content: @Composable () -> Unit,
)
```

Minimal call:
```kotlin
var showSheet by remember { mutableStateOf(false) }

OverlayBottomSheet(
    show = showSheet,
    title = "Select Option",
    onDismissRequest = { showSheet = false },
) {
    // Sheet content
}
```

---

## List Popups

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayListPopup.kt`, `window/WindowListPopup.kt`, `basic/ListPopup.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.overlay.OverlayListPopup
import top.yukonga.miuix.kmp.window.WindowListPopup
import top.yukonga.miuix.kmp.basic.ListPopupColumn
import top.yukonga.miuix.kmp.basic.ListPopupDefaults
import top.yukonga.miuix.kmp.basic.PopupPositionProvider
```

```kotlin
@Composable
fun OverlayListPopup(
    show: Boolean,
    popupModifier: Modifier = Modifier,
    popupPositionProvider: PopupPositionProvider = ListPopupDefaults.DropdownPositionProvider,
    alignment: PopupPositionProvider.Align = PopupPositionProvider.Align.Start,
    enableWindowDim: Boolean = true,
    onDismissRequest: (() -> Unit)? = null,
    onDismissFinished: (() -> Unit)? = null,
    maxHeight: Dp? = null,
    minWidth: Dp = ListPopupDefaults.MinWidth,
    renderInRootScaffold: Boolean = true,
    content: @Composable () -> Unit,
)

@Composable
fun WindowListPopup(
    show: Boolean,
    popupModifier: Modifier = Modifier,
    popupPositionProvider: PopupPositionProvider = ListPopupDefaults.DropdownPositionProvider,
    alignment: PopupPositionProvider.Align = PopupPositionProvider.Align.Start,
    enableWindowDim: Boolean = true,
    onDismissRequest: (() -> Unit)? = null,
    onDismissFinished: (() -> Unit)? = null,
    maxHeight: Dp? = null,
    minWidth: Dp = ListPopupDefaults.MinWidth,
    content: @Composable () -> Unit,
)
```
> [!TIP]
> Use `ListPopupColumn` inside `content` to automatically size the popup width according to the widest child item.

---

## Snackbars

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Snackbar.kt`  
**Imports:**
```kotlin
import top.yukonga.miuix.kmp.basic.Snackbar
import top.yukonga.miuix.kmp.basic.SnackbarHost
import top.yukonga.miuix.kmp.basic.SnackbarHostState
import top.yukonga.miuix.kmp.basic.SnackbarDuration
import top.yukonga.miuix.kmp.basic.SnackbarResult
```

```kotlin
@Composable
fun SnackbarHost(
    state: SnackbarHostState,
    modifier: Modifier = Modifier,
    canSwipeToDismiss: Boolean = true,
    content: @Composable (SnackbarData) -> Unit = { Snackbar(it) },
)

@Composable
fun Snackbar(
    data: SnackbarData,
    modifier: Modifier = Modifier,
    cornerRadius: Dp = SnackbarDefaults.CornerRadius,
    colors: SnackbarColors = SnackbarDefaults.snackbarColors(),
    insideMargin: PaddingValues = SnackbarDefaults.InsideMargin,
)
```

Minimal call:
```kotlin
val snackbarHostState = remember { SnackbarHostState() }
val scope = rememberCoroutineScope()

// In Scaffold:
Scaffold(
    snackbarHost = { SnackbarHost(snackbarHostState) }
) {
    // Trigger snackbar:
    Button(onClick = {
        scope.launch {
            snackbarHostState.showSnackbar("Saved successfully")
        }
    }) {
        Text("Save")
    }
}
```
