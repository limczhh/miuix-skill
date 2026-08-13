# Overlay and Window Components

## Choose the Host First

| Required lifetime/host | Choose | Integration rule |
|---|---|---|
| Page-scoped popup that follows the current page and its bounds | `Overlay*` | Require the intended ancestor `Scaffold`; reuse it rather than creating an accidental nested host |
| Popup independent of the current page's Scaffold, including cross-page/global UI | `Window*` | Use the Window counterpart; no Scaffold popup host is required |

Resolve standalone versus existing host ownership in [Project setup and theme](setup-and-theme.md). For visibility, dismissal, and integrated action patterns, read [Overlay and Window usage patterns](usage-patterns.md#overlay-and-window-components); verify exact parameters through [Source verification](source-verification.md).

## Dismissal and System Back

- At the current candidate snapshot, dialog, bottom-sheet, and list-popup layouts route system back dismissal to `onDismissRequest`; Window variants also use their platform window host where applicable. The caller must update the controlled `show` state. Always provide the callback for a dismissible popup.
- A manual `BackHandler`/`NavigationBackHandler` is normally unnecessary and can compete with the component's own handler. Add one only for an explicit product rule such as a non-dismissible confirmation, and verify that it does not double-handle back or predictive-back gestures.
- `onDismissFinished` runs after the hide animation completes, not when dismissal is merely requested, and may be skipped if the hide is cancelled by showing the popup again. Use it for delayed cleanup; use `onDismissRequest` for the state transition.
- Do not depend on undocumented stacking order. Prefer one active page popup; if a deliberate parent/child flow uses two, close the child first and test back, focus, scrim, and accessibility on every target platform.
- The candidate adds `WindowNavigationEventScope` in `miuix-ui/.../utils/`. Window dialogs, sheets, and list/cascading popups apply it automatically so `NavigationBackHandler`/predictive-back handlers resolve against the focused platform window. Wrap custom separate-window hosts that contain the same handlers.
- Animated dialog, sheet, and list-popup layouts place their library `graphicsLayer` before the caller's `modifier`; caller draw/transform modifiers therefore follow the component animation. Keep this ordering when building a wrapper and do not add a second corrective transform.

## Overlay Components (require Scaffold wrapper)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDialog | `docs/components/overlaydialog.md` | `docs/demo/OverlayDialogDemo.kt` | `miuix-ui/.../overlay/OverlayDialog.kt` |
| OverlayBottomSheet | `docs/components/overlaybottomsheet.md` | `docs/demo/OverlayBottomSheetDemo.kt` | `miuix-ui/.../overlay/OverlayBottomSheet.kt` |
| OverlayListPopup | `docs/components/overlaylistpopup.md` | `docs/demo/OverlayListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayListPopup.kt` |
| OverlayCascadingListPopup | `docs/components/overlaycascadinglistpopup.md` | `docs/demo/OverlayCascadingListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayCascadingListPopup.kt` |

## Window Components (standalone popups, no Scaffold needed)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| WindowDialog | `docs/components/windowdialog.md` | `docs/demo/WindowDialogDemo.kt` | `miuix-ui/.../window/WindowDialog.kt` |
| WindowBottomSheet | `docs/components/windowbottomsheet.md` | `docs/demo/WindowBottomSheetDemo.kt` | `miuix-ui/.../window/WindowBottomSheet.kt` |
| WindowListPopup | `docs/components/windowlistpopup.md` | `docs/demo/WindowListPopupDemo.kt` | `miuix-ui/.../window/WindowListPopup.kt` |
| WindowCascadingListPopup | `docs/components/windowcascadinglistpopup.md` | `docs/demo/WindowCascadingListPopupDemo.kt` | `miuix-ui/.../window/WindowCascadingListPopup.kt` |

## Dialog sizing and large screens

In the current candidate snapshot, `OverlayDialog` and `WindowDialog` both accept:

- `maxWidth: Dp = DialogDefaults.MaxWidth` to prevent dialogs from stretching across tablet/desktop windows.
- `largeScreen: Boolean? = null` to override the centered scale/fade versus bottom-attached presentation; `null` uses the window-size detection.
- `cornerRadius: Dp? = null` to override the centered radius or bottom-attached screen-radius-derived value.

`DialogDefaults.MaxWidth` existed before the candidate; the new public `maxWidth` parameter exposes that limit for both dialog wrappers. `DialogDefaults.CornerRadius` is new. Use both Defaults when a product does not have a deliberate design value. Keep the dialog's content and dismissal state controlled by the caller; these parameters do not remove the need for a `Scaffold` ancestor for `OverlayDialog`.

The default large-screen breakpoint is 840 dp wide and 480 dp high. Centered dialogs use the 32 dp `CornerRadius`; bottom-attached dialogs derive a radius from the screen corner and clamp it to 32..48 dp unless `cornerRadius` is supplied.
