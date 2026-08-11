# Component Selection

## Scenario → Component Quick Map

When the user describes a *scenario* rather than a component name, use this table to short-circuit the lookup — no need to fetch `docs/components/index.md` first.

| User says | Likely components |
|-----------|-------------------|
| "设置页 / 偏好设置 / settings" | `Scaffold` + `SwitchPreference` + `CheckboxPreference` + `ArrowPreference` + `SliderPreference` |
| "弹窗 / 对话框 / dialog" | `OverlayDialog` (in-page) or `WindowDialog` (global) |
| "底部弹出 / bottom sheet" | `OverlayBottomSheet` (in-page) or `WindowBottomSheet` (global) |
| "下拉选择 / dropdown / spinner" | `OverlayDropdownPreference` / `OverlaySpinnerPreference` (in settings) or `OverlayDropdownMenu` (standalone) |
| "搜索 / search" | `SearchBar` |
| "表单 / form / 登录" | `TextField` + `Button` + `Switch` |
| "列表弹出 / popup menu" | `OverlayListPopup` / `OverlayCascadingListPopup` |
| "导航栏 / navigation" | `NavigationBar` (bottom) or `NavigationRail` (side) |
| "标签页 / tabs" | `TabRow` |
| "提示气泡 / tooltip" | `TooltipBox` + `PlainTooltip` / `RichTooltip` |
| "角标 / 徽章 / badge" | `Badge` + `BadgedBox`; for navigation, use `NavigationBarItem(badge = { ... })` or `FloatingNavigationBarItem(badge = { ... })` |
| "滑块 / slider" | `Slider` (basic) or `SliderPreference` (in settings) |

## Component Reference Table

When the user asks about a component, first match it case-insensitively against this table, then read files at the depth appropriate to the user's intent.

### Scaffold

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| Scaffold | `docs/components/scaffold.md` | `docs/demo/ScaffoldDemo.kt` | `miuix-ui/.../basic/Scaffold.kt` |

### Basic Components (miuix-ui)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| Surface | `docs/components/surface.md` | `docs/demo/SurfaceDemo.kt` | `miuix-ui/.../basic/Surface.kt` |
| TopAppBar | `docs/components/topappbar.md` | `docs/demo/TopAppBarDemo.kt` | `miuix-ui/.../basic/TopAppBar.kt` |
| NavigationBar | `docs/components/navigationbar.md` | `docs/demo/NavigationBarDemo.kt` | `miuix-ui/.../basic/NavigationBar.kt` |
| NavigationRail | `docs/components/navigationrail.md` | `docs/demo/NavigationRailDemo.kt` | `miuix-ui/.../basic/NavigationRail.kt` |
| TabRow | `docs/components/tabrow.md` | `docs/demo/TabRowDemo.kt` | `miuix-ui/.../basic/TabRow.kt` |
| Card | `docs/components/card.md` | `docs/demo/CardDemo.kt` | `miuix-ui/.../basic/Card.kt` |
| BasicComponent | `docs/components/basiccomponent.md` | `docs/demo/BasicComponentDemo.kt` | `miuix-ui/.../basic/Component.kt` |
| Button | `docs/components/button.md` | `docs/demo/ButtonDemo.kt` | `miuix-ui/.../basic/Button.kt` |
| IconButton | `docs/components/iconbutton.md` | `docs/demo/IconButtonDemo.kt` | `miuix-ui/.../basic/IconButton.kt` |
| Text | `docs/components/text.md` | `docs/demo/TextDemo.kt` | `miuix-ui/.../basic/Text.kt` |
| SmallTitle | `docs/components/smalltitle.md` | `docs/demo/SmallTitleDemo.kt` | `miuix-ui/.../basic/SmallTitle.kt` |
| TextField | `docs/components/textfield.md` | `docs/demo/TextFieldDemo.kt` | `miuix-ui/.../basic/TextField.kt` |
| Switch | `docs/components/switch.md` | `docs/demo/SwitchDemo.kt` | `miuix-ui/.../basic/Switch.kt` |
| Checkbox | `docs/components/checkbox.md` | `docs/demo/CheckboxDemo.kt` | `miuix-ui/.../basic/Checkbox.kt` |
| RadioButton | `docs/components/radiobutton.md` | `docs/demo/RadioButtonDemo.kt` | `miuix-ui/.../basic/RadioButton.kt` |
| Slider | `docs/components/slider.md` | `docs/demo/SliderDemo.kt` | `miuix-ui/.../basic/Slider.kt` |
| NumberPicker | `docs/components/numberpicker.md` | `docs/demo/NumberPickerDemo.kt` | `miuix-ui/.../basic/NumberPicker.kt` |
| ProgressIndicator | `docs/components/progressindicator.md` | `docs/demo/ProgressIndicatorDemo.kt` | `miuix-ui/.../basic/ProgressIndicator.kt` |
| Snackbar | `docs/components/snackbar.md` | `docs/demo/SnackbarDemo.kt` | `miuix-ui/.../basic/Snackbar.kt` |
| Icon | `docs/components/icon.md` | `docs/demo/IconDemo.kt` | `miuix-ui/.../basic/Icon.kt` |
| FloatingActionButton | `docs/components/floatingactionbutton.md` | `docs/demo/FloatingActionButtonDemo.kt` | `miuix-ui/.../basic/FloatingActionButton.kt` |
| FloatingToolbar | `docs/components/floatingtoolbar.md` | `docs/demo/FloatingToolbarDemo.kt` | `miuix-ui/.../basic/FloatingToolbar.kt` |
| Divider | `docs/components/divider.md` | `docs/demo/DividerDemo.kt` | `miuix-ui/.../basic/Divider.kt` |
| PullToRefresh | `docs/components/pulltorefresh.md` | `docs/demo/PullToRefreshDemo.kt` | `miuix-ui/.../basic/PullToRefresh.kt` |
| SearchBar | `docs/components/searchbar.md` | `docs/demo/SearchBarDemo.kt` | `miuix-ui/.../basic/SearchBar.kt` |
| ColorPalette | `docs/components/colorpalette.md` | `docs/demo/ColorPaletteDemo.kt` | `miuix-ui/.../basic/ColorPalette.kt` |
| ColorPicker | `docs/components/colorpicker.md` | `docs/demo/ColorPickerDemo.kt` | `miuix-ui/.../basic/ColorPicker.kt` |
| ScrollBar | — (no dedicated doc) | — (no dedicated demo) | `miuix-ui/.../basic/ScrollBar.kt` |
| Tooltip | `docs/components/tooltip.md` | `docs/demo/TooltipDemo.kt` | `miuix-ui/.../basic/Tooltip.kt` |
| Badge | `docs/components/badge.md` | `docs/demo/BadgeDemo.kt` | `miuix-ui/.../basic/Badge.kt` |
