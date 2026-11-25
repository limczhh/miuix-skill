# Overlay and Window Components

Use this reference only for Overlay versus common `miuix-ui` Window host, dismissal, back behavior, and popup lifecycle. It does not define page structure or exact signatures. For the separate Desktop JVM native window frame module, use [Desktop Window Components](desktop-window.md). For the optional Glass popup family, use [Glass Components](glass.md).

## Choose the Host First

| Required lifetime/host | Choose | Integration rule |
|---|---|---|
| Page-scoped popup that follows the current page and its bounds | `Overlay*` | Require the intended ancestor `Scaffold`; reuse it rather than creating an accidental nested host. If that host is nested, verify or set `renderInRootScaffold = false` |
| Popup independent of the current page's Scaffold, including cross-page/global UI | `Window*` | Use the Window counterpart; no Scaffold popup host is required |

Use the table above to choose the host family. For page ownership, use the `miuix-guidelines` Skill; open an API reference only if the task contains an exact-signature question.

In the current source-audit snapshot, Overlay components that expose `renderInRootScaffold` default it to `true`, which routes their popup state to the root Scaffold. That default is appropriate for a full-screen application overlay, but it is not a safe assumption for a page-local overlay below nested Scaffolds. Confirm the declaration for the target version and set the flag explicitly when the page host must own the surface.

## Dismissal and System Back

- At the current source-audit snapshot, dialog, bottom-sheet, and list-popup layouts route system back dismissal to `onDismissRequest`; common `Window*` variants also use their platform window host where applicable. The caller must update the controlled `show` state. Always provide the callback for a dismissible popup.
- A manual `BackHandler`/`NavigationBackHandler` is normally unnecessary and can compete with the component's own handler. Add one only for an explicit product rule such as a non-dismissible confirmation, and verify that it does not double-handle back or predictive-back gestures.
- `onDismissFinished` runs after the hide animation completes, not when dismissal is merely requested, and may be skipped if the hide is cancelled by showing the popup again. Use it for delayed cleanup; use `onDismissRequest` for the state transition.
- Do not depend on undocumented stacking order. Prefer one active page popup; if a deliberate parent/child flow uses two, close the child first and test back, focus, scrim, and accessibility on every target platform.
- The current source provides `WindowNavigationEventScope` in `miuix-ui/.../utils/`. Common Window dialogs, sheets, and list/cascading popups apply it automatically so `NavigationBackHandler`/predictive-back handlers resolve against the focused platform window. Wrap custom separate-window hosts that contain the same handlers. `LocalWindowComponentFrame` (`WindowComponentFrame` in `miuix-ui/.../window/WindowComponentHost.kt`) also constrains `Window*` overlays, `Tooltip`, and list popups to custom desktop/window insets and corners when provided.
- `WindowListPopup` and `WindowCascadingListPopup` always clamp menu height to the available safe-area space after margins, even when an explicit `maxHeight` is passed.
- Animated dialog, sheet, and list-popup layouts place their library `graphicsLayer` before the caller's `modifier`; caller draw/transform modifiers therefore follow the component animation. Keep this ordering when building a wrapper and do not add a second corrective transform. Dialog and sheet layouts reset predictive-back progress on open/close and release touch blocking before the exit tail completes.

## Overlay Components (require Scaffold wrapper)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDialog | `docs/components/basic/overlaydialog.md` | `docs/demo/OverlayDialogDemo.kt` | `miuix-ui/.../overlay/OverlayDialog.kt` |
| OverlayBottomSheet | `docs/components/basic/overlaybottomsheet.md` | `docs/demo/OverlayBottomSheetDemo.kt` | `miuix-ui/.../overlay/OverlayBottomSheet.kt` |
| OverlayListPopup | `docs/components/basic/overlaylistpopup.md` | `docs/demo/OverlayListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayListPopup.kt` |
| OverlayCascadingListPopup | `docs/components/basic/overlaycascadinglistpopup.md` | `docs/demo/OverlayCascadingListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayCascadingListPopup.kt` |

## Window Components (standalone popups, no Scaffold needed)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| WindowDialog | `docs/components/basic/windowdialog.md` | `docs/demo/WindowDialogDemo.kt` | `miuix-ui/.../window/WindowDialog.kt` |
| WindowBottomSheet | `docs/components/basic/windowbottomsheet.md` | `docs/demo/WindowBottomSheetDemo.kt` | `miuix-ui/.../window/WindowBottomSheet.kt` |
| WindowListPopup | `docs/components/basic/windowlistpopup.md` | `docs/demo/WindowListPopupDemo.kt` | `miuix-ui/.../window/WindowListPopup.kt` |
| WindowCascadingListPopup | `docs/components/basic/windowcascadinglistpopup.md` | `docs/demo/WindowCascadingListPopupDemo.kt` | `miuix-ui/.../window/WindowCascadingListPopup.kt` |

## Dialog sizing and large screens

In the current source-audit snapshot, `OverlayDialog` and `WindowDialog` both accept:

- `maxWidth: Dp = DialogDefaults.MaxWidth` to prevent dialogs from stretching across tablet/desktop windows.
- `largeScreen: Boolean? = null` to override the centered scale/fade versus bottom-attached presentation; `null` uses the window-size detection.
- `cornerRadius: Dp? = null` to override the centered radius or bottom-attached screen-radius-derived value.

The public `maxWidth` parameter exposes `DialogDefaults.MaxWidth` as the default limit for both dialog wrappers. Use `DialogDefaults.MaxWidth` and `DialogDefaults.CornerRadius` when a product does not have a deliberate design value. Keep the dialog's content and dismissal state controlled by the caller; these parameters do not remove the need for a `Scaffold` ancestor for `OverlayDialog`.

The default large-screen breakpoint is 840 dp wide and 480 dp high. Centered dialogs use the 32 dp `CornerRadius`; bottom-attached dialogs derive a radius from the screen corner and clamp it to 32..48 dp unless `cornerRadius` is supplied.

`OverlayDialog` / `WindowDialog` accept `motion: DialogMotion = DialogMotion.Default` (`Default` or `Glass`), and `OverlayBottomSheet` / `WindowBottomSheet` / public `BottomSheetOverlayLayout` accept `motion: SheetMotion = SheetMotion.Default` (`Default` or `Glass`). On bottom sheets, the header reserves space whenever any of `title`, `startAction`, or `endAction` is non-null (including `title = ""`), while the drag handle is unaffected.

`OverlayDialog` / `OverlayBottomSheet` expose `dimColor` and typed `surface` slots. `DialogSurfaceScope` provides radius, animated scrim and scale; `BottomSheetSurfaceScope` provides radius, animated scrim and overscroll-extension status. Their Window counterparts do not expose these slots. Cascading list popups and icon cascading menus have separate Overlay/Window surface overloads taking `(Shape) -> Modifier`. Use the matching [Glass surface helper](glass.md#glass-overlays-and-setting-dropdowns) (paired with `motion = DialogMotion.Glass` / `SheetMotion.Glass`) when adopting Glass without changing the host.

## Glass overlay boundary

`GlassDialog`, `GlassBottomSheet`, and `GlassDropdownPreference` are not common `Overlay*` aliases. They are in `miuix-glass`, accept a `Backdrop?`, and use the same `Scaffold` popup host boundary while replacing the panel surface with Glass material. Keep the recorded page behind the surface, exclude the popup host and scrim, and use [Glass Components](glass.md) for the exact call shape.

## Common Window versus Desktop Window

The `WindowDialog`/`WindowBottomSheet`/`WindowListPopup` rows above are common Miuix popup components in `miuix-ui/window`. `miuix-window` is a different Desktop JVM module that replaces Compose `Window`/`DialogWindow` and provides native caption bars; use [Desktop Window Components](desktop-window.md) for that API.
