# Miuix Library Map

Use this reference when the task asks what a part of Miuix contains, names an unfamiliar source package, or requires a tour of the pinned repository. It is an index, not a replacement for API contracts. Read the row that matches the task, then follow its focused reference; do not read the whole map and every linked document by default.

The map covers the `commonMain` source areas present in all eight library modules in Miuix `v0.9.4`, pinned to commit `39c40f99844227b853f0049a0933b1f3ae6c00ba`: `miuix-ui`, `miuix-preference`, `miuix-nav`, `miuix-blur`, `miuix-shader`, `miuix-squircle`, `miuix-core`, and `miuix-icons`. It is complete as a source-area index, not as a symbol catalog. A source directory is not automatically a public import boundary: confirm visibility, package, overload, platform source set, and opt-in requirements before writing code.

## How to use the map

1. Match the question to a module and source area below.
2. Open the focused reference in the route column, if one exists.
3. Verify the requested symbol in source when the focused reference does not contain its declaration.
4. Treat `internal`, generated, platform-specific, and implementation-only paths as behavior evidence, not as importable API.
5. Stop when the task has the public symbol, module/import boundary, host or lifecycle rule, and version evidence it needs.

## Coverage labels

The `Coverage` column describes how far this repository routes the investigation; it is not a substitute for API verification:

| Label | Meaning | Code consequence |
|---|---|---|
| **Catalogued** | A public family or focused guide is named | It may identify the next component route, but the declaration still controls the call |
| **Mapped** | The source area is included so an unfamiliar symbol can be located | Inspect visibility, package, module, overload, defaults, and opt-in before importing |
| **Internal** | The path supports public behavior but is not a public entry point | Do not import it or expose it as a component |
| **Platform-sensitive** | The behavior depends on a target/platform implementation | Verify the target source set and runtime platform before claiming support |

Only the `Verified API Contracts` reference provides a maintained fast path for exact declarations, and it remains subordinate to the pinned source and target-project compilation.

## Repository source areas

| Module and source area | What it covers | Route | Coverage |
|---|---|---|---|
| `miuix-ui/basic` | Core controls and page-facing components: app bars, navigation bars, buttons, text, cards, fields, indicators, lists, pull-to-refresh, tabs, toolbars, tooltips, badges, and related defaults | [Component selection](component-selection.md), then [Verified API contracts](api-contracts.md) | Catalogued; exact coverage depends on the symbol |
| `miuix-ui/overlay` | Page-hosted dialogs, bottom sheets, list popups, menus, and overlay hosts | [Overlay and Window catalog](overlays-and-windows.md) | Catalogued |
| `miuix-ui/window` | Independent window dialogs, sheets, popups, and window-scoped hosts | [Overlay and Window catalog](overlays-and-windows.md) | Catalogued |
| `miuix-ui/theme` | `MiuixTheme`, color scheme, typography, shapes, theme controller, and composition locals | [Project setup and theme](setup-and-theme.md), [Color semantics and visual lookup](color-lookup.md) | Catalogued |
| `miuix-ui/color` | Color APIs, core color roles, and color-space helpers under `color/api`, `color/core`, and `color/space` | [Color semantics and visual lookup](color-lookup.md) | Mapped; verify uncovered declarations |
| `miuix-ui/layout` | Layout helpers and layout-facing primitives used by components | [Source verification](source-verification.md) | Mapped; verify public boundary |
| `miuix-ui/icon/basic` | Basic Miuix icon declarations and icon-facing helpers | [Styling, icons, and effects](styling-icons-and-effects.md) | Catalogued |
| `miuix-ui/icon` | Icon infrastructure shared by the basic icon area | [Styling, icons, and effects](styling-icons-and-effects.md) | Mapped; verify symbol visibility |
| `miuix-ui/utils` | Utility modifiers and support functions used by public components | [Source verification](source-verification.md) | Mapped; verify public boundary |
| `miuix-ui/anim` | Animation helpers and component animation support | [Styling, icons, and effects](styling-icons-and-effects.md) | Mapped; verify public boundary |
| `miuix-ui/interfaces` | Shared interfaces and contracts used across Miuix components | [Source verification](source-verification.md) | Mapped; often support-only |
| `miuix-preference/preference` | Preference rows, summaries, switches, checkboxes, radio rows, sliders, and preference internals | [Preference and menu guide](preferences-and-menus.md) | Catalogued; `preference/internal` is implementation-only |
| `miuix-preference/menu` | Dropdown, spinner, menu, and anchored menu behavior | [Preference and menu guide](preferences-and-menus.md) | Catalogued |
| `miuix-preference/popup` | Preference-related popup and popup positioning support | [Preference and menu guide](preferences-and-menus.md) | Mapped; verify public boundary |
| `miuix-nav/nav/core` | Navigation keys, display, controllers, and core navigation contracts | [miuix-nav guide](miuix-nav.md) | Catalogued |
| `miuix-nav/nav/runtime` | Runtime navigation state and destination rendering | [miuix-nav guide](miuix-nav.md) | Catalogued |
| `miuix-nav/nav/state` | Back-stack and navigation state models | [miuix-nav guide](miuix-nav.md) | Catalogued |
| `miuix-nav/nav/transition` | Navigation transitions and transition effects | [miuix-nav guide](miuix-nav.md) | Catalogued |
| `miuix-nav/nav/gesture` | Navigation gesture integration and gesture-driven transitions | [miuix-nav guide](miuix-nav.md) | Mapped; verify target behavior |
| `miuix-blur/blur` | Blur modifiers and blur surfaces | [Styling, icons, and effects](styling-icons-and-effects.md) | Catalogued |
| `miuix-blur/blur/highlight` | Highlight and blur-highlight support | [Styling, icons, and effects](styling-icons-and-effects.md) | Mapped; verify public boundary |
| `miuix-blur/blur/sensor` | Sensor-backed blur behavior | [Styling, icons, and effects](styling-icons-and-effects.md) | Platform-sensitive; verify source and target |
| `miuix-blur/blur/internal` | Blur implementation and platform plumbing | [Source verification](source-verification.md) | Internal; do not import directly |
| `miuix-shader/shader` | Shader-backed visual effects | [Styling, icons, and effects](styling-icons-and-effects.md) | Platform-sensitive; verify source and target |
| `miuix-squircle/squircle` | Squircle shapes and modifiers | [Styling, icons, and effects](styling-icons-and-effects.md) | Catalogued |
| `miuix-squircle/squircle/internal` | Squircle implementation support | [Source verification](source-verification.md) | Internal; do not import directly |
| `miuix-icons/icon/extended` | Extended icon declarations | [Styling, icons, and effects](styling-icons-and-effects.md) | Catalogued; verify icon module dependency |
| `miuix-core/icon` | Shared icon infrastructure | [Styling, icons, and effects](styling-icons-and-effects.md) | Mapped; verify public boundary |
| `miuix-core/utils` | Shared core utilities | [Source verification](source-verification.md) | Mapped; verify public boundary |

## Evidence routes

| Question | Start here | Then stop at |
|---|---|---|
| Does Miuix provide this kind of component? | This map, then [Component selection](component-selection.md) | A matching public family and its semantic boundary |
| What is the exact call, default, or import? | [Verified API contracts](api-contracts.md) | The declaration and module/import contract |
| The symbol is not covered by the contract sheet | [Source verification](source-verification.md) | Source plus docs/Demo/Example evidence appropriate to the claim |
| How should several known components form a page? | Hand the composition question to `miuix-guidelines` | Its page skeleton or composition recipe; return here only for exact calls |
| Is this path public or internal? | [Source verification](source-verification.md) | Visibility, package, source set, and dependency evidence |
| Does the answer change between pinned releases? | [Migration notes](release-v0.9.4.md) | The changed declaration in the target source |

## Stop rule

This map is deliberately broad enough to cover the pinned repository's common source areas, but ordinary implementation work should not become a repository tour. Once one focused route answers the current question, stop loading unrelated component families and keep page composition in `miuix-guidelines`.
