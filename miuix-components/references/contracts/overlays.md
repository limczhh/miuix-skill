# Overlay and Window Dialog Contracts

This is a focused, source-verified API contract guide for the maintained Miuix snapshot: upstream main at commit d8efe1872c08c863a8b33e7044dd2a8b43038032 (v0.9.4-80-gd8efe187, not a release tag).

Use when the task needs the exact OverlayDialog or WindowDialog signature, host, dismissal, or lifecycle contract.

Do not use when the task only needs to choose page-scoped versus independent-window presentation without writing a call.

Before copying a call:

- Check the target project's resolved Miuix version, source set, and dependency substitution.
- Treat the declaration, package/import, module, defaults, state/callback direction, and host requirement as one contract.
- An omitted parameter is not permission to invent an overload or transfer a Material parameter.
- source-verified means the pinned declaration was checked; compile-verified requires compiling the affected target.

The exact source path is part of each contract below. If the target version differs, re-run the source-verification gate before presenting version-sensitive code.
## Dialogs

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayDialog.kt`, `window/WindowDialog.kt`  
**Documentation:** `docs/components/basic/overlaydialog.md`, `docs/components/basic/windowdialog.md`
**Demo:** `docs/demo/src/commonMain/kotlin/OverlayDialogDemo.kt`, `WindowDialogDemo.kt`

Miuix imports:

```kotlin
import top.yukonga.miuix.kmp.overlay.OverlayDialog
import top.yukonga.miuix.kmp.layout.DialogSurfaceScope
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

Use `OverlayDialog` for a surface owned by the current page/shell. Use `WindowDialog` when the surface must be independent of the page `Scaffold`. In both cases, `show` and the dismissal callback are two sides of the same state owner; do not rely on an implicit close behavior. The current `OverlayDialog` also exposes `dimColor` and a `surface` slot; `surface` receives the resolved dialog state through `DialogSurfaceScope` and is not a replacement for the popup host.
