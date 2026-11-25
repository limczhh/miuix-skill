# Desktop Window Components

Use this reference only for Miuix's Desktop JVM window frame, native caption bar, dragging, resize, or platform backdrop integration. Do not use it for `WindowDialog`, `WindowBottomSheet`, or `WindowListPopup` from `miuix-ui`; those are common Miuix popup components and remain in [Overlay and Window Components](overlays-and-windows.md).

The current source evidence is Miuix `v0.9.5-rc01` at `542463a0863b393a3d6052237daae6c3da217e70`.

## Module and platform boundary

`miuix-window` is a `desktopMain` module for Compose Desktop/JVM on Windows, macOS, and Linux. It is not a commonMain component and must not be placed in Android, iOS, Web, or shared code without a platform boundary.

```kotlin
kotlin {
    sourceSets {
        desktopMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-window:0.9.5-rc01")
        }
    }
}
```

The module depends on `miuix-core`, `miuix-squircle`, and `miuix-ui`, and uses JNA for native integrations. Desktop window frames automatically provide `LocalWindowComponentFrame` (`WindowComponentFrame` with client insets and frame corner radii) so `Window*` dialogs, sheets, list popups, and `Tooltip` stay bounded within custom frame chrome. Read the upstream [Window & Title Bar guide](https://github.com/compose-miuix-ui/miuix/blob/542463a0863b393a3d6052237daae6c3da217e70/docs/guide/miuix-window.md) for Linux JVM module-open and release-packaging requirements.

## Public family

| Need | Public symbol | Source |
|---|---|---|
| Main native-styled window | `MiuixWindow` | `miuix-window/src/desktopMain/kotlin/top/yukonga/miuix/kmp/window/MiuixWindow.kt` |
| Dialog native-styled window | `MiuixDialogWindow` | `MiuixDialogWindow.kt` |
| Caption-bar container with native-button safe padding | `MiuixCaptionBar` | `MiuixCaptionBar.kt` |
| Drag region | `WindowDraggableArea` | `WindowDraggableArea.kt` |
| Window properties and platform material | `MiuixWindowProperties`, `MiuixWindowBackgroundType` | `MiuixWindowProperties.kt`, `MiuixWindowBackgroundType.kt` |
| Caption controls | `MiuixCaptionButton*` | `basic/MiuixCaptionButton.kt` and related files |

## Verified composition shape

`MiuixTheme` belongs outside `MiuixWindow`, so the window frame, caption controls, and content share the same theme:

```kotlin
MiuixTheme {
    MiuixWindow(
        state = state,
        onCloseRequest = ::exitApplication,
        title = "Miuix Desktop",
        properties = MiuixWindowProperties.default(),
    ) {
        Column(Modifier.fillMaxSize()) {
            Box(Modifier.fillMaxWidth()) {
                WindowDraggableArea()
                MiuixCaptionBar {
                    // Title and custom caption content.
                }
            }
            // Main content.
        }
    }
}
```

Relevant public shapes:

```kotlin
@Composable
fun MiuixWindow(
    onCloseRequest: () -> Unit,
    state: WindowState = rememberWindowState(),
    visible: Boolean = true,
    title: String = "Untitled",
    icon: Painter? = null,
    decoration: WindowDecoration = WindowDecoration.SystemDefault,
    transparent: Boolean = false,
    resizable: Boolean = true,
    enabled: Boolean = true,
    focusable: Boolean = true,
    alwaysOnTop: Boolean = false,
    properties: MiuixWindowProperties<ComposeWindow> = MiuixWindowProperties.default(),
    onPreviewKeyEvent: (KeyEvent) -> Boolean = { false },
    onKeyEvent: (KeyEvent) -> Boolean = { false },
    init: (ComposeWindow) -> Unit = {},
    content: @Composable FrameWindowScope.() -> Unit,
)

@Composable
fun MiuixCaptionBar(
    modifier: Modifier = Modifier,
    color: Color = Color.Transparent,
    contentColor: Color = Color.Unspecified,
    content: @Composable BoxScope.() -> Unit,
)

@Composable
fun WindowDraggableArea(
    modifier: Modifier = Modifier,
)
```

`MiuixDialogWindow` follows the same public option set but uses Compose Desktop's dialog types and scope:

```kotlin
@Composable
fun MiuixDialogWindow(
    onCloseRequest: () -> Unit,
    state: DialogState = rememberDialogState(),
    visible: Boolean = true,
    title: String = "Untitled",
    icon: Painter? = null,
    decoration: WindowDecoration = WindowDecoration.SystemDefault,
    transparent: Boolean = false,
    resizable: Boolean = true,
    enabled: Boolean = true,
    focusable: Boolean = true,
    alwaysOnTop: Boolean = false,
    properties: MiuixWindowProperties<ComposeDialog> = MiuixWindowProperties.default(),
    onPreviewKeyEvent: (KeyEvent) -> Boolean = { false },
    onKeyEvent: (KeyEvent) -> Boolean = { false },
    init: (ComposeDialog) -> Unit = {},
    content: @Composable DialogWindowScope.() -> Unit,
)
```

The window property object is also public and caller-owned. Its important current fields are `minSize`, `captionBarHeight`, `captionButtonHeight`, `backgroundType`, `captionButtonsVisible`, `moveable`, and the platform visibility/resize callbacks. The source enforces `captionButtonHeight <= captionBarHeight` for both `MiuixWindow` and `MiuixDialogWindow`; keep that relation when overriding `MiuixWindowProperties.default(...)`.

## Hard host constraints

- `WindowDraggableArea` must be inside `MiuixWindow` or `MiuixDialogWindow`; outside that scope it throws.
- A window scope permits at most one active `WindowDraggableArea`. Child Miuix windows have independent registrations. Keep the draggable area outside overlapping `AnimatedContent`, `Crossfade`, or `AnimatedVisibility` branches so outgoing and incoming regions do not coexist.
- `MiuixCaptionBar` reserves the platform caption-button width through `LocalMiuixWindowInfo`; do not manually place title content under native buttons.
- `MiuixWindowProperties.captionButtonHeight` must be less than or equal to `captionBarHeight`.
- Native background types such as Mica, Acrylic, or Vibrancy are platform-sensitive. Always provide `None`/opaque content fallback and validate on the supported desktop OS.

When the task is about page layout inside the window, return to `miuix-guidelines`; this reference only establishes the desktop window host and its exact public boundary.
