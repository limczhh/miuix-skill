---
name: miuix
description: "Miuix (HyperOS) Compose UI 组件库专家。当用户要求用 Miuix 构建 UI、提到小米/HyperOS 设计风格、或引用任何 Miuix 组件名时触发此技能。触发关键词包括但不限于: NavigationBar, NavigationRail, SwitchPreference, Scaffold, OverlayDialog, ArrowPreference, SliderPreference, Tooltip, TooltipBox, PlainTooltip, RichTooltip, Badge, BadgedBox, TextButton, Snackbar, TopAppBar, SearchBar, 设置页, 偏好设置, 弹窗, 对话框, 底部弹出, 下拉选择, 搜索栏, 导航栏, 侧边导航, 标签页, 滑块, 开关, 复选框, 单选按钮, 卡片, 按钮, 文本框, 浮动操作按钮, 工具栏, 进度条, 消息条, 分割线, 颜色选择器, 下拉菜单, 级联菜单, HyperOS 风格 UI, 小米风格 UI。在 miuix-main 项目中, Miuix 是默认 UI 工具包 — 除非用户明确要求 Material 3 / MD3 / 其他设计系统, 否则所有 UI 工作都使用此技能。涵盖: 项目搭建 (Gradle 依赖配置)、主题配置 (ThemeController, 深色模式, 动态颜色)、组件选择、API 参考、代码示例、版本迁移。当用户要求标准 Compose Material 组件、Jetpack Compose Material 3、或明确要求非 Miuix 设计系统时, 不使用此技能。"
version: "0.9.3"
---

# Miuix Skill

## What Miuix Is

Miuix is a Compose Multiplatform UI component library that implements Xiaomi HyperOS design language. It targets Android, iOS, Desktop (JVM), macOS, and Web (Wasm/JS) from a single Kotlin codebase.

**Modules:**
| Module | Purpose | Dependency |
|--------|---------|------------|
| `miuix-ui` | Core UI components — Button, Switch, TextField, NavigationBar, Scaffold, dialogs, etc. | — (root module, includes miuix-core, miuix-squircle) |
| `miuix-preference` | Settings-screen components — SwitchPreference, CheckboxPreference, SliderPreference, dropdown selectors, etc. | depends on miuix-ui |
| `miuix-icons` | 100+ extended icons in 5 weights (Light/Normal/Regular/Medium/Demibold) | includes miuix-core |
| `miuix-blur` | Backdrop blur effects (Android minSdk=33) | includes miuix-shader |
| `miuix-shader` | RuntimeShader abstraction layer (cross-platform shader support) | — (transitively included by miuix-blur / miuix-squircle) |
| `miuix-squircle` | Squircle shape — continuous-curvature corners that look smoother than RoundedCornerShape on screen. Auto-falls back to RoundedCornerShape below API 33. | — (transitively included by miuix-ui) |
| `miuix-navigation3-ui` | Jetpack Navigation 3 UI integration | depends on miuix-ui |

Most projects only need to declare `miuix-ui` (and optionally `miuix-preference`). The rest are transitively included.

**Key concepts:**
- Every Miuix UI must be wrapped in `MiuixTheme { ... }` which provides `MiuixTheme.colorScheme.*` and `MiuixTheme.textStyles.*`
- Overlay components (OverlayDialog, OverlayBottomSheet, etc.) render inside `Scaffold`'s popup layer — they follow the page lifecycle and need a `Scaffold` ancestor. Window components (WindowDialog, WindowBottomSheet, etc.) render in an independent window layer — they're standalone, ideal for cross-page global popups.
- Components follow a consistent API pattern: required params → `modifier` → boolean flags → visual params (cornerRadius, colors, etc.) → content lambda
- Each component has a `ComponentDefaults` object with default dimensions, corner radii, and a `@Composable` color factory function

### Quick Start

**Compose Multiplatform project** (most common — Android, iOS, Desktop, Web):

```kotlin
// settings.gradle.kts — ensure mavenCentral() is in the repositories block
// build.gradle.kts (commonMain)
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.3")
implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.3") // optional, for settings screens
```

**Pure Android project:**

```kotlin
// build.gradle.kts — ensure mavenCentral() is in the repositories block
implementation("top.yukonga.miuix.kmp:miuix-ui-android:0.9.3")
```

### MiuixTheme Setup

Use `ThemeController` to support system dark mode / dynamic colors. Do NOT hardcode `lightColorScheme()` — it locks the app to light theme.

```kotlin
@Composable
fun App() {
    val controller = remember { ThemeController(ColorSchemeMode.System) }
    MiuixTheme(controller = controller) {
        Scaffold(
            topBar = { /* TopAppBar, NavigationBar, etc. */ },
            bottomBar = { /* NavigationBar, TabRow, etc. */ },
            floatingActionButton = { /* FloatingActionButton */ },
            floatingToolbar = { /* FloatingToolbar */ }
        ) {
            // Screen content
        }
    }
}
```

`ColorSchemeMode` options: `System` / `Light` / `Dark` / `MonetSystem` / `MonetLight` / `MonetDark`.

> **Version pinning**: File paths and component mappings reflect source tree at tag `v0.9.3`. For full setup details (multiplatform, ProGuard, baseline profiles), read `docs/guide/getting-started.md`.

## Path Convention

All file paths in the tables below are relative to the Miuix repository root. Resolve them using GitHub URLs:

| Source | URL pattern |
|--------|------------|
| Read a **single file** | `https://raw.githubusercontent.com/compose-miuix-ui/miuix/v0.9.3/<file-path>` |
| Browse a **directory** | `https://github.com/compose-miuix-ui/miuix/tree/v0.9.3/<dir-path>` |
| Rendered **docs** site | `https://compose-miuix-ui.github.io/miuix/<path>` (strip `.md`; `index.md` → `/`) |

| Abbreviation | Relative path |
|---|---|
| `miuix-ui/.../basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/` |
| `miuix-ui/.../overlay/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/overlay/` |
| `miuix-ui/.../window/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/window/` |
| `miuix-ui/.../layout/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/layout/` |
| `miuix-ui/.../theme/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/` |
| `miuix-ui/.../utils/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/utils/` |
| `miuix-ui/.../icon/basic/` | `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/basic/` |
| `miuix-preference/.../preference/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/preference/` |
| `miuix-preference/.../menu/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/menu/` |
| `miuix-preference/.../popup/` | `miuix-preference/src/commonMain/kotlin/top/yukonga/miuix/kmp/popup/` |
| `miuix-core/.../icon/` | `miuix-core/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/` |
| `miuix-icons/.../extended/` | `miuix-icons/src/commonMain/kotlin/top/yukonga/miuix/kmp/icon/extended/` |
| `miuix-shader/.../shader/` | `miuix-shader/src/commonMain/kotlin/top/yukonga/miuix/kmp/shader/` |
| `miuix-squircle/.../squircle/` | `miuix-squircle/src/commonMain/kotlin/top/yukonga/miuix/kmp/squircle/` |
| `docs/components/` | `docs/components/` |
| `docs/demo/` | `docs/demo/src/commonMain/kotlin/` |

---

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

## Tiered Reading Strategy

The depth of reading depends on the user's intent — don't read more than needed. Before reading files, classify the request:

| Intent | Signs | Read |
|---|---|---|
| **Project setup** | "How do I add Miuix to my project", "What's the Gradle dependency", "How to set up the theme" | `docs/guide/getting-started.md` — then follow the setup steps it describes |
| **Vague need** | "I want a settings page", "I need a popup menu", "build a login form" — describes a *scenario*, not a component name | Match in **Scenario → Component Quick Map** first; if no match, read `docs/components/index.md` |
| **Quick info** | "What is X", "What parameters does it have", "What components exist" | Doc only |
| **Usage help** | "How do I use X", "Give me an example", "Write a page with X" | Doc + Demo |
| **Deep dive** | "How is X implemented internally", "Why does it behave this way", debugging | Doc + Demo + Source |

If unsure, start with the doc — it's cheap, and you can always read more if the user's need proves deeper.

---

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


### Overlay Components (require Scaffold wrapper)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDialog | `docs/components/overlaydialog.md` | `docs/demo/OverlayDialogDemo.kt` | `miuix-ui/.../overlay/OverlayDialog.kt` |
| OverlayBottomSheet | `docs/components/overlaybottomsheet.md` | `docs/demo/OverlayBottomSheetDemo.kt` | `miuix-ui/.../overlay/OverlayBottomSheet.kt` |
| OverlayListPopup | `docs/components/overlaylistpopup.md` | `docs/demo/OverlayListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayListPopup.kt` |
| OverlayCascadingListPopup | `docs/components/overlaycascadinglistpopup.md` | `docs/demo/OverlayCascadingListPopupDemo.kt` | `miuix-ui/.../overlay/OverlayCascadingListPopup.kt` |

### Window Components (standalone popups, no Scaffold needed)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| WindowDialog | `docs/components/windowdialog.md` | `docs/demo/WindowDialogDemo.kt` | `miuix-ui/.../window/WindowDialog.kt` |
| WindowBottomSheet | `docs/components/windowbottomsheet.md` | `docs/demo/WindowBottomSheetDemo.kt` | `miuix-ui/.../window/WindowBottomSheet.kt` |
| WindowListPopup | `docs/components/windowlistpopup.md` | `docs/demo/WindowListPopupDemo.kt` | `miuix-ui/.../window/WindowListPopup.kt` |
| WindowCascadingListPopup | `docs/components/windowcascadinglistpopup.md` | `docs/demo/WindowCascadingListPopupDemo.kt` | `miuix-ui/.../window/WindowCascadingListPopup.kt` |

### Preference Components (miuix-preference)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| ArrowPreference | `docs/components/arrowpreference.md` | `docs/demo/ArrowPreferenceDemo.kt` | `miuix-preference/.../preference/ArrowPreference.kt` |
| SwitchPreference | `docs/components/switchpreference.md` | `docs/demo/SwitchPreferenceDemo.kt` | `miuix-preference/.../preference/SwitchPreference.kt` |
| CheckboxPreference | `docs/components/checkboxpreference.md` | `docs/demo/CheckboxPreferenceDemo.kt` | `miuix-preference/.../preference/CheckboxPreference.kt` |
| RadioButtonPreference | `docs/components/radiobuttonpreference.md` | `docs/demo/RadioButtonPreferenceDemo.kt` | `miuix-preference/.../preference/RadioButtonPreference.kt` |
| OverlayDropdownPreference | `docs/components/overlaydropdownpreference.md` | `docs/demo/OverlayDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/OverlayDropdownPreference.kt` |
| OverlaySpinnerPreference | `docs/components/overlayspinnerpreference.md` | `docs/demo/OverlaySpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/OverlaySpinnerPreference.kt` |
| WindowDropdownPreference | `docs/components/windowdropdownpreference.md` | `docs/demo/WindowDropdownPreferenceDemo.kt` | `miuix-preference/.../preference/WindowDropdownPreference.kt` |
| WindowSpinnerPreference | `docs/components/windowspinnerpreference.md` | `docs/demo/WindowSpinnerPreferenceDemo.kt` | `miuix-preference/.../preference/WindowSpinnerPreference.kt` |
| SliderPreference | `docs/components/sliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |
| RangeSliderPreference | `docs/components/rangesliderpreference.md` | — | `miuix-preference/.../preference/SliderPreference.kt` |

### Dropdown Menu Components (miuix-preference/menu/)

| Component | Doc | Demo | Source |
|-----------|-----|------|--------|
| OverlayDropdownMenu | `docs/components/overlaydropdownmenu.md` | `docs/demo/OverlayDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayDropdownMenu.kt` |
| OverlayIconDropdownMenu | `docs/components/overlayicondropdownmenu.md` | `docs/demo/OverlayIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconDropdownMenu.kt` |
| OverlayIconCascadingDropdownMenu | `docs/components/overlayiconcascadingdropdownmenu.md` | `docs/demo/OverlayIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/OverlayIconCascadingDropdownMenu.kt` |
| WindowDropdownMenu | `docs/components/windowdropdownmenu.md` | `docs/demo/WindowDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowDropdownMenu.kt` |
| WindowIconDropdownMenu | `docs/components/windowicondropdownmenu.md` | `docs/demo/WindowIconDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconDropdownMenu.kt` |
| WindowIconCascadingDropdownMenu | `docs/components/windowiconcascadingdropdownmenu.md` | `docs/demo/WindowIconCascadingDropdownMenuDemo.kt` | `miuix-preference/.../menu/WindowIconCascadingDropdownMenu.kt` |

---

## Guide & Theme Resources

| Topic | Guide | Key Source Files |
|-------|-------|-----------------|
| Getting Started | `docs/guide/getting-started.md` | — |
| Icons | `docs/guide/icons.md` | `miuix-core/.../icon/MiuixIcons.kt`, `miuix-ui/.../icon/basic/`, `miuix-icons/.../extended/` |
| Colors | `docs/guide/colors.md` | `miuix-ui/.../theme/Colors.kt` |
| Theme + ThemeController | `docs/guide/theme.md` | `miuix-ui/.../theme/MiuixTheme.kt`, `miuix-ui/.../theme/ThemeController.kt` |
| TextStyles | `docs/guide/textstyles.md` | `miuix-ui/.../theme/TextStyles.kt` |
| Utils | `docs/guide/utils.md` | `miuix-ui/.../utils/` |
| Blur | `docs/guide/blur.md` | `miuix-blur/src/commonMain/kotlin/` |
| Squircle | `docs/guide/squircle.md` | `miuix-squircle/src/commonMain/kotlin/` |
| Multiplatform | `docs/guide/multiplatform.md` | — |
| Navigation3 | `docs/guide/navigation3.md` | — |
| Best Practices | `docs/guide/best-practices.md` | — |
| Component Index | `docs/components/index.md` | — (discovery entry point for "Vague need" scenarios) |
| API Reference | `docs/dokka/index.html` | — (Dokka-generated API signatures, authoritative for exact parameter lists) |

## Supporting Internals

These files underpin multiple components. They're read-on-demand, not listed per component above:

| File | Role |
|------|------|
| `miuix-ui/.../basic/Component.kt` | `BasicComponent` — universal base for all preference and overlay components |
| `miuix-ui/.../utils/MiuixPopupUtils.kt` | Popup position/anchor utilities for all overlay and window popups |
| `miuix-ui/.../layout/DialogContentLayout.kt` | Shared dialog content layout |
| `miuix-ui/.../layout/BottomSheetContentLayout.kt` | Shared bottom sheet content layout |
| `miuix-ui/.../layout/ListPopupLayout.kt` | Shared list popup content layout |
| `miuix-ui/.../layout/CascadingListPopupLayout.kt` | Shared cascading list popup layout |
| `miuix-ui/.../layout/CascadingMorphContent.kt` | Shared cascading morph content |
| `miuix-ui/.../layout/MorphHeaderRow.kt` | Shared morph header row |
| `miuix-preference/.../popup/DropdownEntriesContent.kt` | Internal dropdown entries content |
| `miuix-ui/.../basic/Dropdown.kt` | `DropdownImpl` and `SpinnerItemImpl` — internal dropdown/spinner building blocks used by preference selectors |
| `miuix-ui/.../basic/ListPopup.kt` | `ListPopupContent`, `ListPopupColumn`, `rememberListPopupLayoutInfo` — internal list popup building blocks |

---

## v0.9.2 → v0.9.3 Migration Notes

No breaking API changes. Update the version string and you're done:

```kotlin
// before
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.2")
// after
implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.3")
```

What's new (no code changes required):
- **New components**: `Tooltip` / `PlainTooltip` / `RichTooltip` / `TooltipBox` and `Badge` / `BadgedBox` in `miuix-ui`
- **Navigation badges**: `NavigationBarItem` and `FloatingNavigationBarItem` now accept `badge: (@Composable () -> Unit)?`
- **Expandable NavigationRail**: pass `rememberNavigationRailState()` to `NavigationRail(state = ...)` to enable expand/collapse; leave `state = null` for the classic collapsed rail
- **TextButton typography**: `TextButton` now has `textStyle: TextStyle? = null`
- **Snackbar redesign**: `SnackbarHostState` can stack multiple snackbars; `SnackbarHost` handles layout-driven enter/exit and optional swipe-to-dismiss
- **PullToRefresh sync fix**: `isRefreshing` is the hoisted source of truth; setting it `true` while idle shows the indicator, setting it `false` completes refresh, and a gesture joins an already-running refresh instead of firing a duplicate
- **Icons**: basic icons now include `Close` and `Sidebar`; extended icons add `Home`; many extended icons were optically aligned

Post-0.9.3 main branch note: current `main` also contains `BreadcrumbBar`, `OverlayDialog` / `WindowDialog` `maxWidth`, and `RadioButtonPreference` color/row-interaction changes. Do not document those as v0.9.3 APIs unless targeting `main` or a later release tag.

---

## Example App

`example/` is a full Compose Multiplatform app built with Miuix. It's the best place to see how components, icons, colors, and themes work together in a real application context.

| Directory | What's inside |
|-----------|--------------|
| `example/shared/src/commonMain/kotlin/component/` | One demo screen per component — real usage with MiuixTheme, Scaffold, and navigation |
| `example/shared/src/commonMain/kotlin/` | App entry point, theme setup, main navigation graph |

When the user asks "what does this look like in a real app" or you want to verify how components compose together, browse the relevant screen under `example/shared/.../component/` via GitHub.

---

## Guardrails

These rules prevent the most common failure mode: fabricating APIs that don't exist.

### Why this matters

Miuix is an independent library — its API surface is completely different from Material Design, Material 3, or any other Compose library. Guessing parameter names, icon names, or color tokens from memory of those other libraries will produce code that doesn't compile. Always verify against the actual source files.

Specifically:
- **Icon names**: 7 basic icons (ArrowRight, ArrowUpDown, Check, Close, Search, SearchCleanup, Sidebar — Regular weight only) + 100+ extended icons in 5 weights (Light, Normal, Regular, Medium, Demibold). `MiuixIcons.SomeName` defaults to Regular; other weights use `MiuixIcons.Light.SomeName`, etc. Authoritative sources: `docs/guide/icons.md` (list), `miuix-ui/.../icon/basic/<Name>.kt` and `miuix-icons/.../extended/<Name>.kt` (verify).
- **v0.9.3 changed APIs to verify**: `NavigationBarItem.badge`, `FloatingNavigationBarItem.badge`, `NavigationRailState` / `rememberNavigationRailState`, `TextButton.textStyle`, `SnackbarHostState`, and `PullToRefresh` `isRefreshing` synchronization. Read the relevant doc + source before giving migration-sensitive examples.
- **Parameter names & API signatures**: Docs give overview, demos give runnable usage, source files are authoritative. For simple usage, docs + demos suffice; for parameters not covered by demos or uncertain signatures, read the source.
- **Color token names**: Only use properties defined in `MiuixTheme.colorScheme.*`, located in `miuix-ui/.../theme/Colors.kt`.
- **TextStyle names**: Only use properties defined in `MiuixTheme.textStyles.*`, located in `miuix-ui/.../theme/TextStyles.kt`.

### Always do

- **Wrap in MiuixTheme + Scaffold**: See Key concepts above — every screen needs `MiuixTheme`, overlay components need `Scaffold`.
- **Read before writing**: When the user asks to use a component, read at least the doc. When they ask for a concrete code example, read the doc + demo. When they ask about internals or edge-case behavior, read doc + demo + source.
- **Follow Miuix API conventions when writing code**: All Miuix composables follow a consistent signature pattern — required parameters first, then `modifier: Modifier = Modifier`, then boolean flags, then visual parameters (cornerRadius, colors, etc.), then content lambda last. Each component has a `ComponentDefaults` companion object with standard dimensions and a `@Composable` color factory. Use `MiuixTheme.colorScheme.*` for colors and `MiuixTheme.textStyles.*` for typography.
- **Prefer SquircleShape over RoundedCornerShape**: Miuix provides `SquirclePath` / `SquircleBackground` / `SquircleBorder` from `miuix-squircle` for continuous-curvature corners that look smoother than standard rounded corners. They auto-fall back to `RoundedCornerShape` below API 33. Use `RoundedCornerShape` only when squircle is not available or not needed.
