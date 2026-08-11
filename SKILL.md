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

## References

Read only the reference files required for the task:

- [Project setup and theme](references/setup-and-theme.md)
- [Scenario map and basic component catalog](references/component-selection.md)
- [Overlay and window component catalog](references/overlays-and-windows.md)
- [Preference and dropdown menu catalog](references/preferences-and-menus.md)
- [Guide and theme resources](references/styling-icons-and-effects.md)
- [Path conventions, supporting internals, and example app](references/source-verification.md)
- [v0.9.2 → v0.9.3 migration notes](references/release-v0.9.3.md)

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
