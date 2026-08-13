# Color Semantics and Visual Lookup

Use this reference when a user describes a color visually—“蓝色主按钮”“浅色卡片背景”“灰色摘要文字”“红色警告”，但还不知道对应的 Miuix 参数或 Token 名称。证据固定于上游 tag `v0.9.4-rc01`，提交为 `4a6b750b578880146876e4ab77097d9b01702413`。

Contents: [rules](#lookup-rules) · [visual lookup](#visual-to-semantic-lookup) · [component defaults](#component-defaults-first) · [core roles](#core-color-roles) · [theme and dynamic color](#theme-and-dynamic-color) · [evidence](#source-evidence)

## Lookup rules

1. Identify both the hue and the job of the color: container/fill, text/icon on that container, selected state, error state, border, divider, or disabled state.
2. If a public component owns the visual, use its `*Defaults` color factory first. A component may intentionally use a role such as `tertiaryContainer` rather than the visually similar `primaryContainer`.
3. For custom wrappers or drawing, read the role from `MiuixTheme.colorScheme.<token>`.
4. Pair a container with its `on*` foreground role. `onPrimary` means content on `primary`; it is not a second kind of primary blue.
5. Do not treat the default light-theme hue as the permanent meaning of a token. Monet/dynamic themes and custom `Colors` can change the rendered hue while preserving the semantic role.

For a bare request such as “找一个蓝色”，ask what the blue is for. If no context is available, use `primary` as the default candidate for an accent/action color and state that assumption.

This is a practical index of common color-sensitive components, not a promise that every `*Defaults` API is listed. For a named component that is not covered below, inspect that component's tagged source before inventing a color parameter or state mapping.

```kotlin
// Custom content that needs the current theme's accent pair.
val accent = MiuixTheme.colorScheme.primary
val accentContent = MiuixTheme.colorScheme.onPrimary

// A standard component should normally own this mapping.
@Composable
private fun ConfirmButton(onClick: () -> Unit) {
    Button(
        colors = ButtonDefaults.buttonColorsPrimary(),
        onClick = onClick,
    ) { Text("确认") }
}
```

## Visual-to-semantic lookup

The “default light/dark” values below come from `lightColorScheme()` and `darkColorScheme()` in the tagged `Colors.kt`. They apply to the non-Monet defaults only; use the token name in application code.

| User description | First candidate | Default light / dark sample | Interpretation |
|---|---|---|---|
| 蓝色主色、品牌强调色、主要操作色 | `primary` | `#FF3482FF` / `#FF277AF7` | Main accent role; use component primary Defaults for buttons, switches, and sliders |
| 蓝色背景上的文字或图标 | `onPrimary` | white / white | Foreground paired with `primary` |
| Primary 相关容器背景、强调色容器 | `primaryContainer` | `#FF5D9BFF` / `#FF338FE4` | Primary container role; do not use it as a generic selected-state color or assume it is always pale blue under dynamic color |
| `primaryContainer` 上的内容 | `onPrimaryContainer` | white / white | Foreground paired with the primary container |
| 中性灰次要按钮背景 | `secondaryVariant` | `#FFF0F0F0` / `#FF434343` | Current ordinary `ButtonDefaults.buttonColors()` container role |
| 输入框的灰色底 | `secondaryContainer` | `#FFF0F0F0` / `#FF434343` | Current `TextFieldDefaults.textFieldColors()` background role |
| 下拉菜单选中项的浅蓝/深色容器 | `tertiaryContainer` | `#FFEAF2FF` / `#FF2B3B54` | Current Dropdown selected-container role; pair with `onTertiaryContainer` |
| 红色错误、危险，或明确设计为红色的警告状态 | `error` | `#FFE94634` / `#FFF12522` | Miuix's official red status role is `error`; there is no `alert`, `warning`, `success`, or `info` token in the candidate `Colors` source |
| 错误提示背景 | `errorContainer` | `#FFFDF6F4` / `#FF2E0603` | Error container; pair with `onErrorContainer` |
| 黄色警告、成功、信息等非错误状态 | App-owned semantic token or custom `Color` | — | Miuix has no built-in warning/success/info family; define the product meaning explicitly instead of reusing `error` |
| 页面最底层背景 | `background` | white / `#FF242424` | App/page canvas; pair with `onBackground` |
| 普通表面、简单浮层 | `surface` | `#FFF7F7F7` / black | Generic surface role; do not use it automatically for every Card |
| Card 或分组容器背景 | `surfaceContainer` | white / `#FF242424` | Current `CardDefaults.defaultColors()` background |
| SearchBar 内部输入框的灰色胶囊底 | `InputField.color` | `#FFE8E8E8` / `#FF242424` | `InputField` defaults to `surfaceContainerHigh`; the `SearchBar` wrapper itself has no `SearchBar.color` parameter |
| 普通正文、标题文字 | `onBackground`, `onSurface`, or the component default | black / light text | Choose the foreground paired with the actual container |
| 设置项摘要、次要说明文字 | `onSurfaceVariantSummary` | `#99000000` / `#80FFFFFF` | Summary/supporting-text role; prefer `BasicComponentDefaults.summaryColor()` |
| 操作文字、箭头、次要操作图标 | `onSurfaceVariantActions` | `#66000000` / `#66FFFFFF` | Action affordance role; do not use the summary token by habit |
| 边框、轮廓 | `outline` | `#FFD9D9D9` / `#FF404040` | Outline/border role |
| 分割线 | `dividerLine` | `#FFE0E0E0` / `#FF393939` | Divider role; use `Divider` defaults when possible |
| 弹窗、下拉菜单背后的遮罩 | `windowDimming` | `#4D000000` / `#99000000` | Window dimming role; let dialog/popup components own it |
| 禁用的灰色控件 | Component `*Defaults` | varies by component | Use the component's disabled roles; do not guess one universal disabled gray |

### Similar-looking roles that are not interchangeable

- `primary`, `primaryContainer`, and `tertiaryContainer` may all look blue in the default light theme, but their roles differ. Current dropdown entries use `tertiaryContainer` for the selected container and `onTertiaryContainer` for selected content; do not replace that with `primary` solely because the result looks blue.
- `secondary`, `secondaryContainer`, and `surfaceContainer*` are often gray in the default theme, but they represent different component surfaces and state roles. Use the component's Defaults when one exists.
- `disabled*` tokens are state-specific. Do not make a disabled control by applying an arbitrary alpha to its enabled color unless the source contract explicitly does so.

## Component Defaults first

When the user names a standard component, map the visual request to the public color factory rather than manually assembling the same token pair.

| Component or visual | Public API/default | Source-verified mapping |
|---|---|---|
| 蓝色实心按钮 | `ButtonDefaults.buttonColorsPrimary()` | `primary` + `onPrimary` with `disabledPrimaryButton` + `disabledOnPrimaryButton` |
| 普通按钮 | `ButtonDefaults.buttonColors()` | `secondaryVariant` + `onSecondaryVariant` and their disabled counterparts |
| 蓝色文字按钮 | `ButtonDefaults.textButtonColorsPrimary()` | `primary` text with the primary-button disabled roles |
| 输入框 | `TextFieldDefaults.textFieldColors()` | `secondaryContainer` background, `onSecondaryContainer` label, `primary` focused border |
| Card | `CardDefaults.defaultColors()` | `surfaceContainer` background + `onSurfaceContainer` content |
| BasicComponent / Preference row | `BasicComponentDefaults.titleColor()` / `summaryColor()` | title uses `onBackground`; summary uses `onSurfaceVariantSummary` |
| Slider | `SliderDefaults.sliderColors()` | foreground `primary`, background `sliderBackground`, thumb `onPrimary`, plus slider-specific disabled/key-point roles |
| Dialog | `DialogDefaults` | title `onBackground`, summary `onSurfaceSecondary`, background `background`; dimming uses `windowDimming` |
| Dropdown selected item | dropdown color parameters/defaults | selected container `tertiaryContainer`, selected content/indicator `onTertiaryContainer` |
| NavigationBar selected item | `NavigationBar` implementation defaults | selected content uses `onSurfaceContainer`; unselected content applies the documented alpha to that role |
| Switch | `SwitchDefaults.switchColors()` | checked track/thumb `primary` + `onPrimary`; unchecked track/thumb `secondary` + `onSecondary`; disabled tracks use `disabledPrimary` / `disabledSecondary` |
| Checkbox | `CheckboxDefaults.checkboxColors()` | checked background/foreground `primary` + `onPrimary`; unchecked `secondary` + `secondary`; disabled backgrounds use `disabledPrimary` / `disabledSecondary` |
| RadioButton | `RadioButtonDefaults.radioButtonColors()` | selected `primary`; disabled selected `disabledPrimary` |
| RadioButtonPreference | `RadioButtonPreferenceDefaults.radioButtonPreferenceColors()` | unselected title/summary use their normal roles; selected title/summary use `primary` by default, while the embedded radio glyph keeps its own `RadioButtonColors` |
| Badge | `BadgeDefaults.containerColor` / `contentColor` | `error` container + `onError` content by default; a red badge is not a generic accent container |
| Snackbar | `SnackbarDefaults.snackbarColors()` | snackbar `onSecondaryVariant` + `secondaryVariant`; action pill `primary` + `onPrimary`; dismiss action `onSurfaceContainerVariant` |
| TabRow | `TabRowDefaults.tabRowColors()` | unselected surface/content `surface` + `onSurfaceVariantSummary`; selected surface/content `surfaceContainer` + `onBackground` |
| Progress indicator | `ProgressIndicatorDefaults.progressIndicatorColors()` | foreground `primary`, disabled foreground `disabledPrimarySlider`, track/background `secondaryContainer` |
| ScrollBar | `ScrollBarDefaults.scrollBarColors()` | `Color.Unspecified` defaults to an `onSurface` thumb and hides the track; supply explicit `thumbColor` / `trackColor` only when needed |
| Plain/Rich Tooltip | `TooltipDefaults` | plain inverse-surface pair `onSecondaryVariant` + `secondaryVariant`; rich container `surfaceContainer`, content `onSurfaceContainerVariant`, title `onSurfaceContainer`, action `primary` |
| FloatingActionButton | `FloatingActionButton(containerColor = ...)` | container defaults to `primary`; its content is inside `Surface`, whose default content color is `onSurface`, so do not assume automatic `onPrimary` pairing |
| BreadcrumbBar | `BreadcrumbBarDefaults.breadcrumbBarColors()` | text `onBackground.copy(.55f)`, highlight `primary`, separator `onSurfaceVariantActions`, backgrounds derive from `onBackground` / `primary` alpha |
| Divider | `DividerDefaults.DividerColor` | `dividerLine` |

The `Switch` mapping has a dynamic-color exception: its unchecked thumb uses `LocalColors.current.onSurface.copy(0.38f)` and its disabled checked thumb uses `LocalColors.current.surface` when dynamic colors are active. Preserve those component-specific defaults instead of reducing every state to one `primary`/`secondary` pair.

For a component with a dedicated color parameter, verify the exact parameter name. For example, the 0.9.4 candidate adds `InputField.color`; it does not add `SearchBar.color` to the `SearchBar` wrapper.

## Core color roles

`Colors` in `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/Colors.kt` exposes these public role families. The table is a semantic index, not a promise that every token should be passed directly to every component.

| Family | Tokens | Use |
|---|---|---|
| Primary | `primary`, `onPrimary`, `primaryVariant`, `onPrimaryVariant`, `primaryContainer`, `onPrimaryContainer` | Main accent, its foreground, variants, and accent containers |
| Error | `error`, `onError`, `errorContainer`, `onErrorContainer` | Error/invalid/danger feedback; no separate alert or warning family exists |
| Secondary | `secondary`, `onSecondary`, `secondaryVariant`, `onSecondaryVariant`, `secondaryContainer`, `onSecondaryContainer`, `secondaryContainerVariant`, `onSecondaryContainerVariant` | Secondary controls and neutral-toned containers |
| Tertiary | `tertiaryContainer`, `onTertiaryContainer`, `tertiaryContainerVariant` | Selected dropdown/container states and tertiary surfaces |
| Page/surface | `background`, `onBackground`, `onBackgroundVariant`, `surface`, `onSurface`, `surfaceVariant` | Page canvas, surfaces, and their foregrounds |
| Surface text | `onSurfaceSecondary`, `onSurfaceVariantSummary`, `onSurfaceVariantActions`, `disabledOnSurface` | Secondary, summary, action, and disabled content roles |
| Surface containers | `surfaceContainer`, `onSurfaceContainer`, `onSurfaceContainerVariant`, `surfaceContainerHigh`, `onSurfaceContainerHigh`, `surfaceContainerHighest`, `onSurfaceContainerHighest` | Card, popup, navigation, search, and layered surface roles |
| Structural | `outline`, `dividerLine`, `windowDimming` | Borders, dividers, and window/popup dimming |
| Slider-specific | `disabledPrimary`, `disabledOnPrimary`, `disabledPrimaryButton`, `disabledOnPrimaryButton`, `disabledPrimarySlider`, `disabledSecondary`, `disabledOnSecondary`, `disabledSecondaryVariant`, `disabledOnSecondaryVariant`, `sliderKeyPoint`, `sliderKeyPointForeground`, `sliderBackground` | Component state and slider track/key-point roles; prefer the component Defaults |

Do not invent `warning`, `alert`, `success`, `info`, `blue`, or `gray` properties on `MiuixTheme.colorScheme`. Those are user-facing descriptions that must be resolved to a real semantic role or a deliberate custom `Color`.

## Theme and dynamic color

- `MiuixTheme.colorScheme` exposes the current `Colors` object inside `MiuixTheme`; it is not a fixed palette.
- `lightColorScheme()` and `darkColorScheme()` provide the fixed non-Monet defaults shown above.
- `ThemeController(ColorSchemeMode.MonetSystem/MonetLight/MonetDark)` can generate roles from a key color or system wallpaper. In Monet mode, `primary` may no longer be blue and `error`/surface roles may also change tone.
- If the user wants a fixed brand blue rather than a theme role, configure `lightColorScheme(primary = ...)` and `darkColorScheme(primary = ...)` or a custom `Colors` scheme, then verify contrast and every paired `on*` role. Do not silently replace a semantic role with a literal color.
- If source and rendered documentation disagree, source at the pinned commit wins. At this candidate, the docs table lists a dark `onError` value that differs from `darkColorScheme()`; use the token and the source signature rather than copying that documentation hex blindly.

## Source evidence

Read these exact files at tag `v0.9.4-rc01` before answering a color-sensitive question:

- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/Colors.kt` — public `Colors` properties and fixed `lightColorScheme()` / `darkColorScheme()` defaults.
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/MiuixTheme.kt` — how `MiuixTheme.colorScheme` is provided and read.
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/ThemeController.kt` — System/Light/Dark/Monet modes and dynamic-color selection.
- `docs/guide/colors.md` and `docs/guide/theme.md` — role descriptions and usage context; source wins for exact signatures or conflicting values.
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Button.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TextField.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/SearchBar.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Component.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Card.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Slider.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Dropdown.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt` — component-specific color Defaults and actual role usage.
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Switch.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Checkbox.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/RadioButton.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Badge.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Snackbar.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TabRow.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/ProgressIndicator.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/ScrollBar.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Tooltip.kt` — stateful controls, status surfaces, transient feedback, and tooltip color Defaults.
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/FloatingActionButton.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Surface.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/BreadcrumbBar.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Divider.kt` — direct color parameters and component-specific structural color Defaults.
- `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/layout/DialogContentLayout.kt` — dialog title, summary, background, and window-dimming Defaults.

When reporting a mapping, include the token or Defaults name, its visual role, the container/foreground pairing where relevant, and the source path used to verify it.
