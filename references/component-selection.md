# Component Selection

Use this catalog after resolving code context in [SKILL.md](../SKILL.md#resolve-code-context). After choosing a component, read its doc/demo/source at the depth defined by the entry Skill. For multi-component pages, continue with [Example-derived usage patterns](usage-patterns.md); for visual customization, use [Design language and Defaults](design-language.md).

Contents: [scenario map](#scenario-component-quick-map) · [component table](#component-reference-table) · [public API boundaries](#public-api-boundaries) · [validation notes](#textfield-validation)

## Scenario → Component Quick Map

When the user describes a *scenario* rather than a component name, use this table to short-circuit the lookup — no need to fetch `docs/components/index.md` first.

| User says | Likely components |
|-----------|-------------------|
| "设置页 / 偏好设置 / settings" | `Scaffold` + `SwitchPreference` + `CheckboxPreference` + `ArrowPreference` + `SliderPreference` |
| "弹窗 / 对话框 / dialog" | `OverlayDialog` (in-page) or `WindowDialog` (global) |
| "确认操作 / 二次确认 / confirm" | `OverlayDialog` (page-scoped) or `WindowDialog` (global) + secondary cancel action + primary confirm action |
| "底部弹出 / bottom sheet" | `OverlayBottomSheet` (in-page) or `WindowBottomSheet` (global) |
| "下拉选择 / dropdown / spinner" | `OverlayDropdownPreference` / `OverlaySpinnerPreference` (in settings) or `OverlayDropdownMenu` (standalone) |
| "搜索 / search" | `SearchBar` |
| "表单 / form / 登录" | `TextField` + `Button`; add `Switch` only for an actual binary option |
| "列表弹出 / popup menu" | `OverlayListPopup` / `OverlayCascadingListPopup` |
| "底部导航 / navigation bar / 侧边导航 / navigation chrome" | `NavigationBar` (bottom), `FloatingNavigationBar` (floating bottom), or `NavigationRail` (side) |
| "浮动导航 / floating navigation bar / floating nav" | `FloatingNavigationBar` + `FloatingNavigationBarItem` |
| "页面路由 / 返回栈 / route / back stack" | `miuix-nav`'s `NavDisplay` + `rememberNavBackStack`; read [the routing reference](miuix-nav.md). Do not mistake navigation chrome for routing architecture |
| "路径导航 / 文件路径 / 目录层级" | `BreadcrumbBar` + `BreadcrumbItem`; use `highlightIndex` for the active segment and `joinToPath()` for path reconstruction |
| "标签页 / tabs" | `TabRow` |
| "提示气泡 / tooltip" | `TooltipBox` + `PlainTooltip` / `RichTooltip` |
| "角标 / 徽章 / badge" | `Badge` + `BadgedBox`; for navigation, use `NavigationBarItem(badge = { ... })` or `FloatingNavigationBarItem(badge = { ... })` |
| "滑块 / slider" | `Slider` (basic) or `SliderPreference` (in settings) |

## Component Reference Table

When the user asks about a component, first match it case-insensitively against this table, then read files at the depth appropriate to the user's intent.

### Compose primitives used with Miuix

`LazyColumn`, `LazyRow`, `Row`, `Column`, `Box`, and `AnimatedVisibility` are Compose primitives, not Miuix components, so they do not get Miuix catalog rows or component docs. Use them to arrange and conditionally show Miuix components. Under `MiuixTheme`, standard Compose scrollables receive the theme's Miuix overscroll factory automatically; read `docs/guide/utils.md` when changing overscroll behavior.

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
| FloatingNavigationBar | `docs/components/navigationbar.md` | `docs/demo/NavigationBarDemo.kt` | `miuix-ui/.../basic/NavigationBar.kt` |
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
| BreadcrumbBar | `docs/components/breadcrumbbar.md` | `docs/demo/BreadcrumbBarDemo.kt` | `miuix-ui/.../basic/BreadcrumbBar.kt` |
| Divider | `docs/components/divider.md` | `docs/demo/DividerDemo.kt` | `miuix-ui/.../basic/Divider.kt` |
| PullToRefresh | `docs/components/pulltorefresh.md` | `docs/demo/PullToRefreshDemo.kt` | `miuix-ui/.../basic/PullToRefresh.kt` |
| SearchBar | `docs/components/searchbar.md` | `docs/demo/SearchBarDemo.kt` | `miuix-ui/.../basic/SearchBar.kt` |
| ColorPalette | `docs/components/colorpalette.md` | `docs/demo/ColorPaletteDemo.kt` | `miuix-ui/.../basic/ColorPalette.kt` |
| ColorPicker | `docs/components/colorpicker.md` | `docs/demo/ColorPickerDemo.kt` | `miuix-ui/.../basic/ColorPicker.kt` |
| VerticalScrollBar / HorizontalScrollBar | — (no dedicated doc/demo) | Integrated use: `example/shared/.../SettingsPage.kt`, `MainPage.kt` | `miuix-ui/.../basic/ScrollBar.kt` |
| Tooltip | `docs/components/tooltip.md` | `docs/demo/TooltipDemo.kt` | `miuix-ui/.../basic/Tooltip.kt` |
| Badge | `docs/components/badge.md` | `docs/demo/BadgeDemo.kt` | `miuix-ui/.../basic/Badge.kt` |

### Public API boundaries

The table above is a catalog of public entry points. Supporting source paths are evidence for behavior, not automatically importable APIs. In particular:

- `TextButton` is a public button API documented and implemented with the Button family; verify its exact source signature before treating it as a separate module or inventing a wrapper.
- `InputField` is the editable-field API nested in the SearchBar documentation/source. `SearchBar` itself does not expose every parameter accepted by `InputField`, including its color parameter.
- `ProgressiveBlur` is a public effect descriptor used by modifier APIs in `miuix-blur`, not a general-purpose `miuix-ui` component; verify the target platform and the exact modifier overload in the blur source.
- Names under [Supporting Internals](source-verification.md#supporting-internals), such as `ListPopupContent` or `DropdownImpl`, are implementation evidence unless the pinned source exports them publicly.

### SmallTitle and section labels

At the current `0.9.4-rc01` candidate snapshot there is no public Miuix `SectionHeader`. `SmallTitle` is the library's auxiliary/category label and the Example normally places it immediately before the related `Card`, not inside the Card as another preference row. If a target project has a `SectionHeader`, inspect it as an application-owned wrapper before replacing it.

### Scrollbar integration

`VerticalScrollBar` and `HorizontalScrollBar` are experimental (`ExperimentalScrollBarApi`). Pair the bar with the same `ScrollState`, `LazyListState`, or `LazyGridState` used by the scrollable through `rememberScrollBarAdapter(state)`. The Example overlays a vertical bar as a sibling of `LazyColumn` inside a `Box`, aligns it to the end edge, and forwards the list's `contentPadding` as `trackPadding`; this keeps the bar from becoming list content or covering padded regions. Prefer this pattern for long desktop/tablet-style content, and omit a visible scrollbar when the product does not need one.

### TextField validation

`TextField` has no `isError` or `supportingText` parameter at the current candidate snapshot. For field, Preference-row, transient-network, and blocking-page failure patterns, read [Error and Failure States](design-language.md#error-and-failure-states) rather than inferring Material parameters.

### Current candidate additions and behavior notes

- `Scaffold` already hosted `floatingToolbar` and `floatingToolbarPosition` before this candidate; use the scaffold slot instead of manually overlaying a toolbar, and rely on the candidate's snackbar spacing fix for a bottom toolbar.
- `InputField` accepts `color`; the `SearchBar` wrapper does not. Pass `Color.Transparent` only when another surface, such as a backdrop blur, intentionally owns the capsule background.
- `PullToRefresh` supports `rememberPullToRefreshState(refreshThreshold = ...)` and `onPullProgress`; read the migration reference before deriving a second refresh state.
