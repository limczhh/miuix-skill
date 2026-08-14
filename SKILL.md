---
name: miuix
description: "Miuix (HyperOS) Compose 专家。用于 Miuix/HyperOS UI 构建、审查、重构；在 Miuix 项目或点名组件/API 时触发：MiuixTheme、ThemeController、miuix-ui、miuix-preference、miuix-blur、miuix-nav；NavigationBar/navigation bar、FloatingNavigationBar/floating navigation bar、NavigationRail、TopAppBar/top app bar、Scaffold、TabRow、Button/TextButton/IconButton、FloatingActionButton/FloatingToolbar、Card、TextField/text field、InputField、SearchBar/search bar、Switch/Checkbox/RadioButton、Slider/slider、SliderPreference/Overscroll、ProgressIndicator、Snackbar、Tooltip/TooltipBox/PlainTooltip/RichTooltip、Badge/BadgedBox、BreadcrumbBar/breadcrumb bar、PullToRefresh/pull to refresh、ColorPicker、OverlayDialog/OverlayBottomSheet/OverlayListPopup、WindowDialog/WindowBottomSheet、ArrowPreference/SwitchPreference/CheckboxPreference/RadioButtonPreference、Dropdown/Spinner、ProgressiveBlur、WindowNavigationEventScope、NavDisplay/NavController/NavKey/NavTransitions/NavDisplayEffects。覆盖设置、弹窗、导航、表单、主题、颜色、无障碍、自适应；指定 Material 3/MD3/其他系统时不触发；无 Miuix 上下文的通用 button/card/slider 不触发。"
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
| `miuix-blur` | Backdrop blur, texture effects, and progressive blur (Android effect paths require RuntimeShader/API 33) | includes miuix-shader |
| `miuix-shader` | RuntimeShader abstraction layer (cross-platform shader support) | — (transitively included by miuix-blur / miuix-squircle) |
| `miuix-squircle` | Squircle shape — continuous-curvature corners that look smoother than RoundedCornerShape on screen. Auto-falls back to RoundedCornerShape below API 33. | — (transitively included by miuix-ui) |
| `miuix-nav` | Self-contained Compose Multiplatform navigation runtime with a serializable back stack, continuous-depth transitions, and predictive/edge back handling | independent of `miuix-ui`; uses `miuix-squircle`, lifecycle/ViewModel, navigation-event, and kotlinx serialization APIs internally |

Most projects start with `miuix-ui` and add `miuix-preference` for settings rows. Add `miuix-icons`, `miuix-blur`, or `miuix-nav` explicitly when those features are used. `miuix-ui` already brings in `miuix-core` and `miuix-squircle`; `miuix-shader` arrives transitively through `miuix-blur` or `miuix-squircle`. `miuix-nav` is the current navigation module; do not infer `androidx.navigation3` APIs from the older module name.

**Key concepts:**
- The application UI subtree must be under an ancestor `MiuixTheme`, which provides `MiuixTheme.colorScheme.*` and `MiuixTheme.textStyles.*`; individual screens should not duplicate the root theme
- Natural-language color requests need role lookup: read [Color semantics and visual lookup](references/color-lookup.md) and map the user's visual description to a verified token or component `*Defaults` factory; do not invent `warning`/`alert` tokens or equate a default blue sample with a permanent semantic hue
- Overlay components (OverlayDialog, OverlayBottomSheet, etc.) render inside `Scaffold`'s popup layer — they follow the page lifecycle and need a `Scaffold` ancestor. Window components (WindowDialog, WindowBottomSheet, etc.) render in an independent window layer — they're standalone, ideal for cross-page global popups.
- `Scaffold` can host a `floatingToolbar` with a `ToolbarPosition`; this slot predates the current candidate, while the candidate fixes bottom-toolbar spacing so the `Snackbar` stays above it.
- `PullToRefresh` keeps `isRefreshing` hoisted, while `rememberPullToRefreshState(refreshThreshold = ...)` owns indicator mechanics and exposes full-range progress for optional observers.
- `miuix-nav` separates route/back-stack ownership from `NavigationBar`/`NavigationRail` chrome. `rememberNavBackStack` requires `@Serializable` route keys for save/restore; the v1 runtime is a flat stack, does not provide dialog scenes or a built-in result channel, and defaults transition input blocking off.
- Treat an exact Miuix English component name or its common spaced form as a direct signal: map `navigation bar`, `floating navigation bar`, `slider`, `search bar`, and similar names to the component catalog before applying generic Compose UI advice.
- Public components generally follow a recognizable parameter order, but exact signatures must be verified in docs/demo/source rather than inferred
- Many configurable components expose a dedicated `*Defaults` object for supported dimensions, shapes, colors, and state styling

## References

Read only the reference files required for the task:

**Workflows**

- [Existing UI review and improvement workflow](references/ui-review-workflow.md)
- [Example-derived usage patterns](references/usage-patterns.md)

**Design and component selection**

- [Design language and component Defaults](references/design-language.md)
- [Color semantics and visual lookup](references/color-lookup.md)
- [Scenario map and basic component catalog](references/component-selection.md)
- [Overlay and window component catalog](references/overlays-and-windows.md)
- [Preference and dropdown menu catalog](references/preferences-and-menus.md)

**Setup and evidence**

- [Project setup and theme](references/setup-and-theme.md)
- [Styling, icons, and effects](references/styling-icons-and-effects.md)
- [Path conventions, supporting internals, and example app](references/source-verification.md)
- [`miuix-nav` routing and transition guide](references/miuix-nav.md)
- [v0.9.3 → v0.9.4-rc01 migration notes](references/release-v0.9.4-rc01.md)
- [Historical v0.9.2 → v0.9.3 migration notes](references/release-v0.9.3.md)

## Resolve Code Context

Decide which layer the requested code owns before writing it:

| Context | Rule | Read next |
|---|---|---|
| **Existing application screen or component** | Inspect the existing root theme and page host. Reuse them; do not add another `MiuixTheme` or `Scaffold` unless the requested behavior needs a new page boundary or popup host. | [Project setup and theme](references/setup-and-theme.md), then the task-specific reference |
| **Standalone runnable `App` or complete example** | Own the root explicitly: create a remembered `ThemeController`, wrap one `MiuixTheme`, and add `Scaffold` only when the example needs page slots, insets, snackbar, or Overlay hosting. | [Project setup and theme](references/setup-and-theme.md) + the selected component/page evidence |
| **Isolated component snippet** | Show the smallest useful call and state its required ancestor context. Include a local `Scaffold` only when the snippet itself must run and host an Overlay. | Component catalog + doc/demo; source for uncertain APIs |

## Working Method

Do not stop after locating a component or source file. Turn the evidence into an implementation:

1. Resolve the target project's Miuix version before using a version-sensitive example: inspect its Gradle files, version catalog, dependency lock, or build output; identify the platform/source set; then choose the matching upstream tag or commit. Treat this Skill's `0.9.4-rc01` snapshot as a fallback reference only when the target version is unknown, and label the mismatch.
2. Resolve the code context above, then understand the user's visual and behavioral goal.
3. Inspect the target project's theme, page shell, hierarchy, state ownership, navigation, insets, and existing conventions when project code is available.
4. For an existing UI review or polish task, follow [UI review and improvement workflow](references/ui-review-workflow.md). Inspect both code and available visual evidence before changing geometry.
5. Classify the request by scenario, then select Miuix components from the relevant catalog.
6. Load evidence at the required depth. For the current catalog, use the pinned web-source paths in [Source verification](references/source-verification.md): read the relevant Markdown doc, demo, Example code, and source from the `0.9.4-rc01` candidate snapshot at commit `4a6b750b`; use the historical `v0.9.3` tag only when the task explicitly targets that release.
7. Before designing or restructuring a page, read [Example-derived usage patterns](references/usage-patterns.md) and [Design language and Defaults](references/design-language.md). Extract the pattern; do not copy the showcase application wholesale.
8. Implement with public Miuix components, semantic theme tokens, and component Defaults first. Customize only for an explicit product need or established project convention.
9. Verify exact APIs against the pinned candidate snapshot in [Source verification](references/source-verification.md). Compile code changes and validate visual changes with a render, screenshot, preview, emulator, or device when available.

## Evidence Loading Strategy

Load only what the request needs:

| Intent | Read | Use it to |
|---|---|---|
| **Project setup** | [Project setup and theme](references/setup-and-theme.md), then `docs/guide/getting-started.md` | Configure modules and the root theme |
| **Single component explanation** | Relevant catalog row + component doc | Explain purpose, states, and parameters |
| **Single component code** | Component doc + matching `docs/demo/<Component>Demo.kt`; read source for uncertain parameters; add [Project setup and theme](references/setup-and-theme.md) if the example owns `App`/theme/host | Produce a minimal, version-correct call in the right ancestor context |
| **Page creation or redesign** | [Example-derived usage patterns](references/usage-patterns.md) + [Design language and Defaults](references/design-language.md) + the closest page or section under `example/shared/`; add setup/theme when the code owns the root | Reuse integrated composition and semantic visual roles without duplicating hosts |
| **Natural-language color or visual styling** | [Color semantics and visual lookup](references/color-lookup.md) + [Design language and Defaults](references/design-language.md) + tagged `Colors.kt` and the selected component's Defaults | Resolve hue plus visual role to a real Miuix token/factory, preserve container/`on*` pairing, and distinguish fixed default samples from dynamic theme output |
| **Existing UI review or polish** | [UI review and improvement workflow](references/ui-review-workflow.md) + [Design language and Defaults](references/design-language.md) + target code and available visual evidence | Diagnose hierarchy, styling, state, inset, adaptive, and accessibility problems before editing |
| **Accessibility, adaptive, or platform-sensitive UI** | [UI review and improvement workflow](references/ui-review-workflow.md) + [Styling, icons, and effects](references/styling-icons-and-effects.md) + target platform evidence | Check semantics, targets, text scaling, RTL, IME, light/dark/dynamic color, and supported platform behavior instead of treating a screenshot as the whole contract |
| **Popup, dialog, or dropdown** | [Overlay and window component catalog](references/overlays-and-windows.md) or [Preference and dropdown menu catalog](references/preferences-and-menus.md) + matching demo + source | Choose the correct host/lifecycle and state model |
| **Navigation architecture** | [Example-derived usage patterns](references/usage-patterns.md) + [`miuix-nav` guide](references/miuix-nav.md) + `example/shared/.../AppState.kt`, `AppContent.kt`, and `navigation/` | Separate `NavDisplay` routing/back-stack architecture from NavigationBar or NavigationRail chrome |
| **Internal behavior or debugging** | [Path conventions and supporting internals](references/source-verification.md) + doc + demo + source | Trace implementation instead of guessing |

### Stop loading evidence

Stop expanding the evidence set when the task has the minimum proof it needs:

- For an explanation, the public purpose and relevant parameters are supported by the pinned documentation and, where behavior is non-obvious, source.
- For a component snippet, the exact signature, imports/dependency, required ancestor, and state/callback contract are known from the doc/demo/source path.
- For a page, the hierarchy, host, state owner, insets, and relevant Example composition are mapped; do not keep reading unrelated component demos.
- For a review, the target code and available visual evidence have been classified, and the applicable static/render/runtime validation level is chosen.
- For a migration, the exact target tag/commit, affected API diff, and target compilation/regression checks are identified.

Reopen already-read references only when a version, platform, or source conflict appears. If the required pinned web evidence is unavailable, stop and report the verification gap instead of silently widening to unpinned sources.

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
- Include dependency lines only when setup is part of the task or the target project is missing them. Respect the target project's Miuix version; this Skill's catalog follows the `0.9.4-rc01` candidate snapshot and must be rechecked when the target uses another version.
- Do not omit callbacks, dismissal paths, disabled behavior, or selected-value updates needed to make the demonstrated interaction work.
- Use Miuix components that match the scenario, consume Scaffold/inset padding, and prefer theme tokens/component Defaults before literal styling. Do not present Example-only helpers as library requirements.
- Report source/API verification, compilation, and visual/device validation as separate facts.

## Guardrails

These rules prevent the most common failure mode: fabricating APIs that don't exist.

### Why this matters

Miuix is an independent library — its API surface is completely different from Material Design, Material 3, or any other Compose library. Guessing parameter names, icon names, or color tokens from memory of those other libraries will produce code that doesn't compile. Always verify against the actual source files.

Apply these non-negotiable checks:

- Read the evidence depth required by the task: component intent from the upstream Markdown doc, isolated usage from its demo when producing a call shape, integrated composition from the closest Example page when composing a page, and uncertain or version-sensitive contracts from the exact snapshot in [Source verification](references/source-verification.md). Do not load every reference or every upstream file by default.
- Treat Example as a reference implementation, not an API contract. Reuse hierarchy and state flow without importing showcase-only helpers or effects.
- When the candidate snapshot has no public Miuix component for the requested interaction, compose it first from Compose Foundation/Layout/Animation primitives under `MiuixTheme`, reuse Miuix semantic tokens and nearby component Defaults, and keep the custom wrapper application-owned. Mix Material or Material 3 only when the target project intentionally does so or the user explicitly requests it. Disclose the custom behavior and verify its semantics, disabled/error states, and platform behavior.
- Verify `BreadcrumbBar`, the existing `Scaffold.floatingToolbar` slot and its candidate snackbar spacing, `InputField.color` (not `SearchBar.color`), `OverlayDialog`/`WindowDialog` `maxWidth`/`largeScreen`/`cornerRadius`, `WindowNavigationEventScope`, `PullToRefresh` progress APIs, `RadioButtonPreference.colors`, and `ProgressiveBlur` before giving migration-sensitive examples.
- For `miuix-nav`, verify `NavKey`, `rememberNavBackStack`, `NavDisplay`'s `entry<T> { ... }` DSL, `NavTransitions`, and `NavDisplayEffects` in the candidate source. Do not fabricate `androidx.navigation3` scene APIs or a built-in result channel.
- Use only color and typography tokens present in tagged `Colors.kt` and `TextStyles.kt`; do not infer Material or Material 3 names.
- For color descriptions such as “蓝色主按钮”“红色警告”“黄色警告”“灰色摘要文字”, read [Color semantics and visual lookup](references/color-lookup.md). Verify the actual `Colors` source and the selected component's Defaults; use `error` only for the candidate's red status role, and use an app-owned semantic token for yellow/non-error warning states instead of fabricating `alert`/`warning`/`success` properties. For named controls such as `Switch`, `Checkbox`, `RadioButton`, `Badge`, `Snackbar`, `TabRow`, `ProgressIndicator`, `ScrollBar`, and `Tooltip`, preserve their state-specific Defaults rather than inferring colors from hue alone.
- For icon dependencies, verified icon names, blur limits, and the actual squircle modifiers, read [Styling, icons, and effects](references/styling-icons-and-effects.md).
