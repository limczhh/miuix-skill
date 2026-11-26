# Overlays, Popups, and Windows

Use for `Overlay*` vs `Window*` lifecycle/host decisions, `DialogMotion`/`SheetMotion`, `ListPopup`, `Snackbar`, `WindowComponentFrame`, and Desktop JVM `miuix-window` frames.

Contents: [Overlay vs Window](#overlay-vs-window-host) · [back, lifecycle & frame bounds](#back-lifecycle-and-frame-bounds) · [Dialogs & Sheets](#dialogs-and-bottom-sheets) · [ListPopups & Snackbars](#list-popups-and-snackbars) · [Desktop miuix-window](#desktop-window-frames-miuix-window)

## Overlay vs Window Host

| Required scope/lifetime | Family | Source & Docs | Integration rule |
|---|---|---|---|
| Scoped to current page & bounds | `Overlay*` | `miuix-ui/.../overlay/`, `docs/components/basic/overlay*.md` | Requires ancestor `Scaffold` popup host; reuse existing host, and set `renderInRootScaffold = false` if nested locally (`true` is default -> root `Scaffold`) |
| Independent of page `Scaffold` / cross-page global UI | `Window*` | `miuix-ui/.../window/`, `docs/components/basic/window*.md` | Standalone platform window/popup layer; no `Scaffold` host required |

Keep all `Overlay*`/`Window*` composables **composed** while `show` becomes `false` so hide animations and `onDismissFinished` complete. (`GlassDialog`, `GlassBottomSheet`, `GlassDropdownPreference` are `Scaffold`-hosted `miuix-glass` components, not `Window*` replacements; see [Glass & blur](glass-and-blur.md)).

## Back, Lifecycle, and Frame Bounds

- **System back & `onDismissRequest`**: Dialog, bottom-sheet, and list-popup layouts route back dismissal to `onDismissRequest`. Let the caller update controlled `show`; avoid adding a competing manual `BackHandler`/`NavigationBackHandler` unless non-dismissible confirmation requires it.
- **`onDismissFinished`**: Invoked after exit animation completes, skipped if hide is cancelled mid-flight by reopening. Use for delayed cleanup/draft reset, not state transitions.
- **`WindowNavigationEventScope`** (`miuix-ui/.../utils/`): Common `Window*` popups apply it automatically so predictive/system back binds to the focused window; wrap custom separate window hosts with it when needed.
- **`LocalWindowComponentFrame` / `WindowComponentFrame`** (`miuix-ui/.../window/WindowComponentHost.kt`): Constrains `Window*` popups/dialogs/sheets, `Tooltip`, and list popups inside custom window client insets and rounded corners.
- **Animation layering & touch**: Layouts apply library `graphicsLayer` before caller `modifier`. Dialog/sheet layouts reset predictive-back progress on open/close and release touch blocking before the exit tail completes. Avoid stacking multiple simultaneous page popups.

## Dialogs and Bottom Sheets

- **Packages**: `Overlay*` (`top.yukonga.miuix.kmp.overlay`), `Window*` (`top.yukonga.miuix.kmp.window`), `DialogDefaults`/`DialogMotion`/`BottomSheetDefaults`/`SheetMotion` (`top.yukonga.miuix.kmp.layout`), `LocalDismissState` (`top.yukonga.miuix.kmp.theme`). Dialog buttons go inside `content` using `TextButton` + `ButtonDefaults` (no `confirmButton`/`dismissButton` args):

```kotlin
OverlayDialog(
    show = showDialog, // Keep composed while show = false
    title = "Confirm action",
    summary = "Apply changes?",
    onDismissRequest = { showDialog = false },
) {
    Row(horizontalArrangement = Arrangement.spacedBy(20.dp)) {
        TextButton(
            text = "Cancel",
            onClick = { showDialog = false },
            modifier = Modifier.weight(1f),
        )
        TextButton(
            text = "Confirm",
            onClick = { onConfirm(); showDialog = false },
            colors = ButtonDefaults.textButtonColorsPrimary(),
            modifier = Modifier.weight(1f),
        )
    }
}
```

- **Dialog sizing (`OverlayDialog` / `WindowDialog`)**:
  - `maxWidth: Dp = DialogDefaults.MaxWidth` bounds tablet/desktop stretch.
  - `largeScreen: Boolean? = null` overrides centered scale/fade vs bottom-attached slide (`null` auto-detects at `840.dp` wide × `480.dp` high). Centered uses `DialogDefaults.CornerRadius` (`32.dp`); bottom-attached derives screen corner radius clamped to `32..48 dp` unless `cornerRadius: Dp?` is set.
- **Motion presets (`rc01`)**:
  - `OverlayDialog` & `WindowDialog` accept `motion: DialogMotion = DialogMotion.Default` (`Default` or `Glass`).
  - `OverlayBottomSheet`, `WindowBottomSheet`, and public `BottomSheetOverlayLayout` (`miuix-ui/.../layout/BottomSheetContentLayout.kt`) accept `motion: SheetMotion = SheetMotion.Default` (`Default` or `Glass`). Bottom sheets extend directly on upward overscroll.
- **Sheet header rule**: On `OverlayBottomSheet`, `WindowBottomSheet`, and `GlassBottomSheet`, the header reserves vertical space if **any** of `title`, `startAction`, or `endAction` is non-null (including `title = ""`); the drag handle is unaffected.
- **Typed `surface` slots (`Overlay*` only)**: `OverlayDialog` (`dimColor`, `DialogSurfaceScope`) and `OverlayBottomSheet` (`dimColor`, `BottomSheetSurfaceScope` with `isOverscroll`) support custom surface adapters; `Window*` counterparts do **not** expose `surface` slots. Pair `glassDialogSurface`/`glassBottomSheetSurface` with `motion = DialogMotion.Glass`/`SheetMotion.Glass`. Cascading list popups & icon cascading menus use separate `(Shape) -> Modifier` surface overloads.

## List Popups and Snackbars

- **`OverlayListPopup` + `ListPopupColumn` + `DropdownImpl`**: Wrap anchor and popup together in a `Box`; keep `OverlayListPopup` composed when `show = false`:

```kotlin
Box {
    TextButton(text = "Select", onClick = { showPopup = true })
    OverlayListPopup(
        show = showPopup,
        alignment = PopupPositionProvider.Align.TopStart,
        onDismissRequest = { showPopup = false },
    ) {
        ListPopupColumn {
            items.forEachIndexed { index, label ->
                DropdownImpl(
                    text = label, optionSize = items.size,
                    isSelected = selectedIndex == index, index = index,
                    onSelectedIndexChange = { selectedIndex = it; showPopup = false },
                )
            }
        }
    }
}
```

- **`ListPopupColumn` sizing**: Public in `miuix-ui/.../basic/ListPopup.kt`. Measures intrinsic widths from **up to the first 8 children** only, clamped to `200..288 dp` within parent constraints — check long labels beyond item 8.
- **Safe-area height cap**: On `WindowListPopup` and `WindowCascadingListPopup`, available safe-area space after margins always caps menu height even when `maxHeight` is explicitly supplied.
- **Snackbar**: `docs/components/basic/snackbar.md` (`miuix-ui/.../basic/Snackbar.kt`). Keep the host outside covered popups/bars.

## Desktop Window Frames (`miuix-window`)

Guide: `docs/guide/miuix-window.md`, sources: `miuix-window/src/desktopMain/kotlin/top/yukonga/miuix/kmp/window/`.

```kotlin
@OptIn(ExperimentalComposeUiApi::class) // Required: MiuixWindow defaults decoration to @ExperimentalComposeUiApi WindowDecoration
@Composable
fun DesktopHost(onClose: () -> Unit, content: @Composable () -> Unit) {
    MiuixTheme {
        val isDark = MiuixTheme.colorScheme.surface.luminance() < 0.5f
        MiuixWindow(
            state = rememberWindowState(),
            onCloseRequest = onClose,
            properties = MiuixWindowProperties.default(captionButtonIsDarkTheme = isDark, backgroundIsDarkTheme = isDark),
        ) {
            val captionHeight = LocalMiuixWindowInfo.current.captionBarHeight
            Column(Modifier.fillMaxSize().background(MiuixTheme.colorScheme.surface)) {
                if (captionHeight > 0.dp) {
                    Box(Modifier.fillMaxWidth().height(captionHeight)) {
                        WindowDraggableArea()
                        MiuixCaptionBar { Text("Title", Modifier.align(Alignment.CenterStart).padding(start = 16.dp)) }
                    }
                }
                Box(Modifier.weight(1f).fillMaxWidth()) { content() }
            }
        }
    }
}
```

- **`desktopMain` JVM only** (Windows, macOS, Linux via JNA; depends on `miuix-core`, `miuix-squircle`, `miuix-ui`). Read the upstream guide for Linux JVM module-open and packaging flags. Desktop frames automatically provide `LocalWindowComponentFrame` so `Window*` overlays stay inside custom client bounds/corners.
- **Public APIs**: `MiuixWindow`, `MiuixDialogWindow`, `MiuixCaptionBar`, `WindowDraggableArea`, `MiuixWindowProperties`, `MiuixWindowBackgroundType`, `MiuixCaptionButton*`.
- **Host hard rules**:
  1. **Always add `@OptIn(ExperimentalComposeUiApi::class)`** (`androidx.compose.ui.ExperimentalComposeUiApi`) on callers of `MiuixWindow` / `MiuixDialogWindow` (required even without passing `decoration`, because `WindowDecoration.SystemDefault` is experimental).
  2. Wrap `MiuixTheme` **outside** `MiuixWindow` / `MiuixDialogWindow` so frame and caption buttons share the theme.
  3. `WindowDraggableArea` throws outside `MiuixWindow` / `MiuixDialogWindow`; allow at most **one** active `WindowDraggableArea` per window (keep outside overlapping `AnimatedContent` / `Crossfade` / `AnimatedVisibility`).
  4. `MiuixCaptionBar` reserves native caption-button clearance via `LocalMiuixWindowInfo`.
  5. Preserve `MiuixWindowProperties.captionButtonHeight <= captionBarHeight`.
  6. Mica / Acrylic / Vibrancy depend on OS capability; keep `None` / opaque fallback readable.
