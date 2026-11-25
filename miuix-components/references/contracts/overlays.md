# Overlay, Window, Sheet, and Popup Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain rc01 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

`Overlay*` components use the Miuix `Scaffold` popup host; `renderInRootScaffold` selects the outermost or current Scaffold. `Window*` uses a separate platform popup/window layer constrained by `LocalWindowComponentFrame` (`WindowComponentFrame`) when provided by desktop frames or custom hosts. Choose through [host and lifecycle notes](../overlays-and-windows.md#choose-the-host-first), then read the matching official doc. Keep the component composed while `show` becomes false so it can complete dismissal; use completed-exit callbacks for cleanup when provided. Dialog/sheet layouts reset predictive-back progress on open/close and release touch blocking before the exit tail completes.

## Dialogs

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayDialog.kt`, `window/WindowDialog.kt`, `layout/DialogMotion.kt`

### OverlayDialog & WindowDialog

Both dialog wrappers accept `motion: DialogMotion = DialogMotion.Default` (`Default` or `Glass`, covering both bottom-attached and centered presentations; pair `motion = DialogMotion.Glass` when using `glassDialogSurface`).

Read the pinned documentation/source: [OverlayDialog](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/overlaydialog.md), [WindowDialog](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/windowdialog.md).

---

## Bottom Sheets

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayBottomSheet.kt`, `window/WindowBottomSheet.kt`, `layout/BottomSheetContentLayout.kt`, `layout/SheetMotion.kt`

### OverlayBottomSheet & WindowBottomSheet

Both sheet wrappers (and public `BottomSheetOverlayLayout`) accept `motion: SheetMotion = SheetMotion.Default` (`Default` or `Glass`; pair `motion = SheetMotion.Glass` when using `glassBottomSheetSurface`). The sheet header reserves vertical space if any of `title`, `startAction`, or `endAction` is non-null (including `title = ""`); the drag handle is unaffected.

Read the pinned documentation/source: [OverlayBottomSheet](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/overlaybottomsheet.md), [WindowBottomSheet](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/windowbottomsheet.md).

---

## List Popups

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/OverlayListPopup.kt`, `window/WindowListPopup.kt`, `basic/ListPopup.kt`

Read the pinned documentation/source: [OverlayListPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/overlaylistpopup.md), [WindowListPopup](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/windowlistpopup.md).
> [!TIP]
> `ListPopupColumn` is public. It uses intrinsic widths from **up to the first 8 children**, then clamps the result to a 200–288 dp band adjusted to the parent constraints. It does not measure every later item to find a global maximum. Check long labels beyond that prefix; use a deliberate custom content layout when the stock sizing does not fit. On `WindowListPopup` and `WindowCascadingListPopup`, available space after margins always caps menu height even when `maxHeight` is passed.

---

## Snackbars

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Snackbar.kt`

Read the pinned documentation/source: [SnackbarHost / Snackbar](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/snackbar.md).
