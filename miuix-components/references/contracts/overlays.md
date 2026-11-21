# Overlay, Window, Sheet, and Popup Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain beta03 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

`Overlay*` components use the Miuix `Scaffold` popup host; `renderInRootScaffold` selects the outermost or current Scaffold. `Window*` uses a separate platform popup/window layer. Choose through [host and lifecycle notes](../overlays-and-windows.md#choose-the-host-first), then read the matching official doc. Keep the component composed while `show` becomes false so it can complete dismissal; use completed-exit callbacks for cleanup when provided.

## Dialogs

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayDialog.kt`, `window/WindowDialog.kt`

### OverlayDialog & WindowDialog

Read the pinned documentation/source: [OverlayDialog](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/overlaydialog.md), [WindowDialog](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/windowdialog.md).

---

## Bottom Sheets

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayBottomSheet.kt`, `window/WindowBottomSheet.kt`

### OverlayBottomSheet & WindowBottomSheet

Read the pinned documentation/source: [OverlayBottomSheet](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/overlaybottomsheet.md), [WindowBottomSheet](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/windowbottomsheet.md).

---

## List Popups

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayListPopup.kt`, `window/WindowListPopup.kt`, `basic/ListPopup.kt`

Read the pinned documentation/source: [OverlayListPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/overlaylistpopup.md), [WindowListPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/windowlistpopup.md).
> [!TIP]
> `ListPopupColumn` is public. It uses intrinsic widths from **up to the first 8 children**, then clamps the result to a 200–288 dp band adjusted to the parent constraints. It does not measure every later item to find a global maximum. Check long labels beyond that prefix; use a deliberate custom content layout when the stock sizing does not fit.

---

## Snackbars

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Snackbar.kt`

Read the pinned documentation/source: [SnackbarHost / Snackbar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/docs/components/basic/snackbar.md).
