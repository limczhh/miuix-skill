---
name: miuix
description: "Miuix (HyperOS) Compose UI 组件库专家。当用户要求用 Miuix 构建、完善、审查或重构 UI，提到小米/HyperOS 设计风格，按截图调整现有 Miuix 界面，或引用任何 Miuix 组件名时触发此技能。触发关键词包括但不限于: NavigationBar, NavigationRail, SwitchPreference, Scaffold, OverlayDialog, ArrowPreference, SliderPreference, Tooltip, TooltipBox, PlainTooltip, RichTooltip, Badge, BadgedBox, TextButton, Snackbar, TopAppBar, SearchBar, 设置页, 偏好设置, 弹窗, 对话框, 底部弹出, 下拉选择, 搜索栏, 导航栏, 侧边导航, 标签页, 滑块, 开关, 复选框, 单选按钮, 卡片, 按钮, 文本框, 浮动操作按钮, 工具栏, 进度条, 消息条, 分割线, 颜色选择器, 下拉菜单, 级联菜单, UI 完善, UI 优化, UI 审查, HyperOS 风格 UI, 小米风格 UI。在 miuix-main 项目中, Miuix 是默认 UI 工具包 — 除非用户明确要求 Material 3 / MD3 / 其他设计系统, 否则所有 UI 工作都使用此技能。涵盖: 项目搭建、主题与视觉语言、组件选择、API 与 Example 证据、现有 UI 诊断、代码实现和版本迁移。当用户明确要求非 Miuix 设计系统时, 不使用此技能。"
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
| `miuix-navigation3-ui` | Jetpack Navigation 3 scene and transition UI | independent of miuix-ui; requires Navigation 3 runtime and uses miuix-squircle internally |

Most projects start with `miuix-ui` and add `miuix-preference` for settings rows. Add `miuix-icons`, `miuix-blur`, or `miuix-navigation3-ui` explicitly when those features are used. `miuix-ui` already brings in `miuix-core` and `miuix-squircle`; `miuix-shader` arrives transitively through `miuix-blur` or `miuix-squircle`.

**Key concepts:**
- The application UI subtree must be under an ancestor `MiuixTheme`, which provides `MiuixTheme.colorScheme.*` and `MiuixTheme.textStyles.*`; individual screens should not duplicate the root theme
- Overlay components (OverlayDialog, OverlayBottomSheet, etc.) render inside `Scaffold`'s popup layer — they follow the page lifecycle and need a `Scaffold` ancestor. Window components (WindowDialog, WindowBottomSheet, etc.) render in an independent window layer — they're standalone, ideal for cross-page global popups.
- Public components generally follow a recognizable parameter order, but exact signatures must be verified in docs/demo/source rather than inferred
- Many configurable components expose a dedicated `*Defaults` object for supported dimensions, shapes, colors, and state styling

## References

Read only the reference files required for the task:

**Workflows**

- [Existing UI review and improvement workflow](references/ui-review-workflow.md)
- [Example-derived usage patterns](references/usage-patterns.md)

**Design and component selection**

- [Design language and component Defaults](references/design-language.md)
- [Scenario map and basic component catalog](references/component-selection.md)
- [Overlay and window component catalog](references/overlays-and-windows.md)
- [Preference and dropdown menu catalog](references/preferences-and-menus.md)

**Setup and evidence**

- [Project setup and theme](references/setup-and-theme.md)
- [Styling, icons, and effects](references/styling-icons-and-effects.md)
- [Path conventions, supporting internals, and example app](references/source-verification.md)
- [v0.9.2 → v0.9.3 migration notes](references/release-v0.9.3.md)

## Resolve Code Context

Decide which layer the requested code owns before writing it:

| Context | Rule | Read next |
|---|---|---|
| **Existing application screen or component** | Inspect the existing root theme and page host. Reuse them; do not add another `MiuixTheme` or `Scaffold` unless the requested behavior needs a new page boundary or popup host. | [Project setup and theme](references/setup-and-theme.md), then the task-specific reference |
| **Standalone runnable `App` or complete example** | Own the root explicitly: create a remembered `ThemeController`, wrap one `MiuixTheme`, and add `Scaffold` only when the example needs page slots, insets, snackbar, or Overlay hosting. | [Project setup and theme](references/setup-and-theme.md) + the selected component/page evidence |
| **Isolated component snippet** | Show the smallest useful call and state its required ancestor context. Include a local `Scaffold` only when the snippet itself must run and host an Overlay. | Component catalog + doc/demo; source for uncertain APIs |

## Working Method

Do not stop after locating a component or source file. Turn the evidence into an implementation:

1. Resolve the code context above, then understand the user's visual and behavioral goal.
2. Inspect the target project's theme, page shell, hierarchy, state ownership, navigation, insets, and existing conventions when project code is available.
3. For an existing UI review or polish task, follow [UI review and improvement workflow](references/ui-review-workflow.md). Inspect both code and available visual evidence before changing geometry.
4. Classify the request by scenario, then select Miuix components from the relevant catalog.
5. Load evidence at the required depth. Read Markdown docs from the `v0.9.3` tag for intent, `docs/demo/` for a minimal call shape, `example/shared/` for integrated composition, and tagged source for the final API contract.
6. Before designing or restructuring a page, read [Example-derived usage patterns](references/usage-patterns.md) and [Design language and Defaults](references/design-language.md). Extract the pattern; do not copy the showcase application wholesale.
7. Implement with public Miuix components, semantic theme tokens, and component Defaults first. Customize only for an explicit product need or established project convention.
8. Verify exact APIs against the pinned `v0.9.3` source. Compile code changes and validate visual changes with a render, screenshot, preview, emulator, or device when available.

## Evidence Loading Strategy

Load only what the request needs:

| Intent | Read | Use it to |
|---|---|---|
| **Project setup** | [Project setup and theme](references/setup-and-theme.md), then `docs/guide/getting-started.md` | Configure modules and the root theme |
| **Single component explanation** | Relevant catalog row + component doc | Explain purpose, states, and parameters |
| **Single component code** | Component doc + matching `docs/demo/<Component>Demo.kt`; read source for uncertain parameters; add [Project setup and theme](references/setup-and-theme.md) if the example owns `App`/theme/host | Produce a minimal, version-correct call in the right ancestor context |
| **Page creation or redesign** | [Example-derived usage patterns](references/usage-patterns.md) + [Design language and Defaults](references/design-language.md) + the closest page or section under `example/shared/`; add setup/theme when the code owns the root | Reuse integrated composition and semantic visual roles without duplicating hosts |
| **Existing UI review or polish** | [UI review and improvement workflow](references/ui-review-workflow.md) + [Design language and Defaults](references/design-language.md) + target code and available visual evidence | Diagnose hierarchy, styling, state, inset, adaptive, and accessibility problems before editing |
| **Popup, dialog, or dropdown** | [Overlay and window component catalog](references/overlays-and-windows.md) or [Preference and dropdown menu catalog](references/preferences-and-menus.md) + matching demo + source | Choose the correct host/lifecycle and state model |
| **Navigation architecture** | [Example-derived usage patterns](references/usage-patterns.md) + `docs/guide/navigation3.md` + `example/shared/.../AppState.kt` and `AppContent.kt` | Separate routing/back-stack architecture from NavigationBar or NavigationRail chrome |
| **Internal behavior or debugging** | [Path conventions and supporting internals](references/source-verification.md) + doc + demo + source | Trace implementation instead of guessing |

## Code Delivery Contract

When the task includes code, this is the authoritative delivery checklist. Reference files add domain-specific guidance; they do not define a second completion contract.

| Requested scope | Deliverable shape |
|---|---|
| **Standalone runnable app** | Exact imports, a complete `@Composable App`, root `ThemeController` + `MiuixTheme`, any required `Scaffold`, and working state/callbacks |
| **Page-level integration** | A complete page `@Composable` with its parameters, hoisted state/callbacks, required imports or explicit existing-context assumptions, and no duplicate application root |
| **Component-level snippet** | The smallest useful call plus the state/callbacks it needs and a one-line ancestor/dependency assumption; do not add an `App` wrapper unless requested |

Before returning code:

- Default to the smallest integration-ready snippet that fulfills the interaction. Return a complete root `App` only when the user asks for a standalone/runnable example or project setup owns the root; include exact imports for standalone code.
- Preserve or explicitly define state ownership. Hoist persistent/business state; keep only transient presentation state such as local popup visibility in `remember`.
- Make theme, `Scaffold`, popup-host, and inset ownership explicit according to **Resolve Code Context**. Never add a bare root `MiuixTheme {}` when the example is responsible for system theme behavior; use the `ThemeController` pattern from setup/theme.
- Include dependency lines only when setup is part of the task or the target project is missing them. Respect the target project's Miuix version; this Skill's API catalog is pinned to `v0.9.3`.
- Do not omit callbacks, dismissal paths, disabled behavior, or selected-value updates needed to make the demonstrated interaction work.
- Use Miuix components that match the scenario, consume Scaffold/inset padding, and prefer theme tokens/component Defaults before literal styling. Do not present Example-only helpers as library requirements.
- Report source/API verification, compilation, and visual/device validation as separate facts.

## Guardrails

These rules prevent the most common failure mode: fabricating APIs that don't exist.

### Why this matters

Miuix is an independent library — its API surface is completely different from Material Design, Material 3, or any other Compose library. Guessing parameter names, icon names, or color tokens from memory of those other libraries will produce code that doesn't compile. Always verify against the actual source files.

Apply these non-negotiable checks:

- Read component intent from the tagged Markdown doc, isolated usage from its demo, integrated composition from the closest Example page, and uncertain or version-sensitive contracts from `v0.9.3` source.
- Treat Example as a reference implementation, not an API contract. Reuse hierarchy and state flow without importing showcase-only helpers or effects.
- When `v0.9.3` has no public Miuix component for the requested interaction, compose it first from Compose Foundation/Layout/Animation primitives under `MiuixTheme`, reuse Miuix semantic tokens and nearby component Defaults, and keep the custom wrapper application-owned. Mix Material or Material 3 only when the target project intentionally does so or the user explicitly requests it. Disclose the custom behavior and verify its semantics, disabled/error states, and platform behavior.
- Verify `NavigationBarItem.badge`, `FloatingNavigationBarItem.badge`, `NavigationRailState`, `TextButton.textStyle`, `SnackbarHostState`, and `PullToRefresh.isRefreshing` before giving migration-sensitive examples.
- Use only color and typography tokens present in tagged `Colors.kt` and `TextStyles.kt`; do not infer Material or Material 3 names.
- For icon dependencies, verified icon names, blur limits, and the actual squircle modifiers, read [Styling, icons, and effects](references/styling-icons-and-effects.md).
