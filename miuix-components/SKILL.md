---
name: miuix-components
description: "Use when developing with Miuix 0.9.5-beta01 and a task needs factual library knowledge: mapping a vague UI need to a component family, surveying a source area, checking availability, exact public API signatures, imports, modules, defaults, host or lifecycle behavior, dependency setup, or source verification. Do not trigger for page composition or visual hierarchy when no library contract is needed."
---

# Miuix Components

## Scope

Miuix is an independent Compose Multiplatform UI library with a Xiaomi/HyperOS visual language. This Skill has two related jobs: orient the agent inside the Miuix library, and verify the public contract needed by an implementation. It does not prescribe a page template, information hierarchy, or product-specific visual direction; use `miuix-guidelines` for those decisions when that Skill is available.

**This Skill supports only Miuix `0.9.5-beta01`**, release tag `v0.9.5-beta01` at commit `6d2554a850b8960e2616c968263ff64beb5ffb03`. Use the full beta coordinate in dependencies and examples. Do not maintain older-version contracts or compatibility branches. A project resolving another Miuix version is outside this Skill's supported scope: report the mismatch and apply this baseline only when a dependency upgrade is authorized. Dependency substitution/local changes still require inspection; they do not make this catalog target-source-verified.

## Joint decision boundary

Use the two Skills as a handoff, not as two competing component catalogs:

| Question | Owner | Handoff rule |
|---|---|---|
| Page skeleton, hierarchy, Insets, Scaffold ownership, composition, or gesture ownership | `miuix-guidelines` | Settle the page boundary and component relationship first |
| Concrete component, parameter, import, module, Defaults, host, lifecycle, or version contract | `miuix-components` | Verify only the symbols the chosen composition will call |
| Both page design and implementation are involved | `miuix-guidelines` → `miuix-components` → `miuix-guidelines` | Structure first, API contract second, composition review last |
| Target resolution differs from beta01 | `miuix-components` | Report the unsupported version or local override; do not infer compatibility or switch this Skill to another release |

Keep the evidence labels separate: `catalog-verified` describes this Skill's pinned snapshot, `target-source-verified` describes the target project's resolved source, and `compile-verified` requires a successful target compilation.

## Natural-language intake

The user does not need to know a Miuix symbol. First translate ordinary wording into a capability and a missing discriminator; do not force the user to choose a component from the catalog.

| User wording | First route | Source-backed discriminator; do not assume it yet |
|---|---|---|
| “想要一个搜索框，输入后能展开结果” | If it is page-level, `miuix-guidelines` first; otherwise [Component selection](references/component-selection.md) | `SearchBar` is the expanded container that renders the supplied content and `InputField` is its editable field; verify whether the request needs expanded result content or only text input |
| “底下放几个页面入口/底栏” | `miuix-guidelines` for shell ownership, then [Component selection](references/component-selection.md) | `NavigationBar` and `FloatingNavigationBar` are the navigation family and document 2–5 items; the visual variant and route owner still depend on context |
| “设置项点开可以选几个值” | [Preference and menu guide](references/preferences-and-menus.md) | First distinguish a complete setting row from a standalone control, then plain dropdown items from richer spinner items; Overlay versus Window is a host/lifetime decision |
| “做一个弹窗/下拉菜单” | [Overlay and Window catalog](references/overlays-and-windows.md) | Decide whether it belongs to the current `Scaffold` or an independent window; do not infer the variant from the word “弹窗” alone |
| “顶部大标题跟着滚动收起来” | `miuix-guidelines` for placement, then [Component selection](references/component-selection.md) | `TopAppBar` uses a scroll behavior and the same `MiuixScrollBehavior` must connect to the primary scroll owner through its nested-scroll contract |
| “下拉刷新和折叠顶栏/回弹一起用” | `miuix-guidelines` for gesture ownership, then [Scroll, pull-to-refresh, and overscroll contracts](references/contracts/scroll-refresh-and-overscroll.md) | Choose one verified owner for the app-bar connection from the target source; do not infer that PullToRefresh is a passive indicator or attach the same connection twice |
| “想要毛玻璃/玻璃底栏/透明弹窗” | [Glass components](references/glass.md) | Decide whether the request is a composed `miuix-glass` surface or only a lower-level `miuix-blur` effect; verify `Backdrop`, fallback, and `Scaffold` popup-host boundaries |
| “想要模糊/背景采样效果” | [Styling, icons, and effects](references/styling-icons-and-effects.md) | Route to `miuix-blur`/`miuix-material`, then check target platform and runtime support before promising the effect |
| “桌面窗口要自定义标题栏” | [Desktop window guide](references/desktop-window.md) | This is `miuix-window` in `desktopMain`, not the common `miuix-ui` `WindowDialog` family |
| “Miuix 有什么可以用” | [Library overview](references/library-overview.md) | Return a capability/module map, not a dump of every declaration |

These are routing cues, not verified call signatures. If the target project is available, inspect its version, source set, dependency, existing host, and theme before asking questions. If the family remains ambiguous, ask only the one discriminator that changes the module, host, or state contract; otherwise state the working assumption and continue to source verification.

When the wording includes page layout, placement, or ownership, `miuix-guidelines` is the primary Skill. This Skill only identifies the Miuix capability needed by that structure and verifies the concrete symbols after the composition boundary is settled.

## Workflow modes

After natural-language intake, classify the request before opening a reference:

- **Library orientation:** the user asks what Miuix is, what modules or component families exist, or what dependency fits a capability. Read [Library overview](references/library-overview.md) first and return a map, not guessed code.
- **Semantic selection:** the capability is known but the appropriate public family is unclear. Read [Component selection](references/component-selection.md), then stop at the smallest matching family.
- **API contract:** the symbol is known and the user needs its real signature, import, module, default, or host rule. Read the [Verified API contract index](references/api-contracts.md), then exactly one matching contract guide; otherwise use [Source verification](references/source-verification.md).
- **Source investigation:** the symbol, source package, or version behavior is unfamiliar. Read [Library map](references/library-map.md) to locate the pinned source area, then verify the declaration.

Do not start an orientation request with the API contract sheet, and do not answer an exact API request by scanning the whole library overview. The workflow should narrow the question before it increases evidence depth.

## Operating contract

- Treat the pinned Miuix source as the authority for maintained catalog facts. Confirm beta01 resolution and inspect its actual dependency graph and local overrides before writing target code.
- Never infer Miuix names or parameters from Material, Material 3, another Compose library, or a similarly named component.
- Keep public API, documentation, Demo, Example, and target-project evidence distinct. Example code demonstrates integration; it is not a public API contract.
- Report catalog evidence, target-source evidence, and compilation as separate facts. Do not use an unqualified `source-verified` label in the final status.
- Do not turn a component's existence or default into a requirement that every page use it.

## Verification labels

Use the strongest label actually supported by the evidence:

| Label | Meaning | What it does not prove |
|---|---|---|
| `catalog-verified` | The declaration or behavior was checked against the maintained pinned snapshot | Target-version compatibility or compilation |
| `target-source-verified` | The target declaration was checked after resolving its published artifact, `includeBuild`, dependency substitution, source set, and relevant checkout state | Successful compilation or runtime behavior |
| `compile-verified` | The affected target compiled with the claimed call | Visual, device, or runtime behavior |
| `runtime-verified` | The relevant runtime or device behavior was exercised | Unrelated platforms, configurations, or versions |
| `API verification blocked` | The target-sensitive declaration cannot be checked because the target source/version evidence is unavailable | Any version-sensitive implementation claim |

When returning an API fact, name the strongest evidence actually checked. A version outside beta01 is a scope mismatch, not evidence that source access is blocked. Inspect local overrides of beta01 separately and state any deviation from the pinned contract.

## Source-first implementation loop

When the task asks for Miuix code, the references are a route into evidence, not a substitute for the pinned source.

1. Inspect the target project's declared Miuix coordinate, platform/source set, dependency substitution or `includeBuild`, resolved source checkout/commit, existing theme, and host before choosing a call. Confirm `0.9.5-beta01`; report an outside-scope version rather than adding compatibility code. If the target project is unavailable, assume this Skill's beta01 baseline and mark target resolution as pending.
2. Choose one reference route below to locate the component or source area.
3. Acquire the declaration through [Source verification](references/source-verification.md#acquire-source-evidence), then verify the public package, module, overload, parameters, defaults, opt-in, and host/lifecycle behavior required by the task.
4. Use docs and Demo for intended usage, and Example or real consumer code only for integrated composition. Do not promote any of them to API authority.
5. Write the smallest call that matches the verified declaration. Do not fill an uncovered API or import by analogy.
6. Compile the affected target when it is available. Report `target-source-verified; compilation pending` when the target source was checked but compilation was not run. If only the maintained snapshot was checked, report `catalog-verified; target compatibility pending`.

## Reference routing

Start with exactly one primary reference. A link is a route, not an instruction to preload every reference. Open a second reference only when the task contains a separate unresolved question.

| Task | Primary reference | Open another reference only when |
|---|---|---|
| What is Miuix, what modules or capability layers does it provide, or which dependency should start a task? | [Library overview](references/library-overview.md) | A semantic role or exact symbol still needs to be selected |
| Survey an unfamiliar Miuix module or source area | [Library map](references/library-map.md) | The map routes to a public component, exact call, or source behavior that the task needs |
| Identify a component family or public capability | [Component selection](references/component-selection.md) | The selected symbol needs an exact call or host contract |
| Exact theme or Scaffold call | [Theme and page host contract](references/contracts/theme-and-host.md) | The resolved beta01 source is customized or the host contract is incomplete |
| Exact top-bar or scroll behavior call | [Top bars and scroll contract](references/contracts/top-bars-and-scroll.md) | The resolved beta01 source is customized or gesture behavior is also involved |
| Exact PullToRefresh, overscroll, or refresh/scroll bridge call | [Scroll, pull-to-refresh, and overscroll contract](references/contracts/scroll-refresh-and-overscroll.md) | The resolved beta01 source is customized or the page has competing gesture owners |
| Exact CrossAxis pager configuration or BreadcrumbBar list state | [Pager and breadcrumb contract](references/contracts/pager-and-breadcrumb.md) | The task also needs saved-mode normalization |
| Exact Card, SmallTitle, or navigation call | [Surface and navigation contract](references/contracts/surfaces-and-navigation.md) | The resolved beta01 source is customized or page ownership is also involved |
| Exact dialog/window call | [Overlay and Window contract](references/contracts/overlays.md) | The resolved beta01 source is customized or the host/lifecycle contract is incomplete |
| Exact preference, input, or button call | [Input and action contract](references/contracts/inputs-and-actions.md) | The resolved beta01 source is customized or the symbol is not covered |
| Find the right contract guide for an exact call | [Verified API contract index](references/api-contracts.md) | The symbol is not listed or the resolved beta01 source is customized |
| Exact call for an uncovered beta01 API, behavior, or local override | [Source verification](references/source-verification.md) | A component catalog or matching evidence is needed to identify the symbol |
| Dependency, theme, root host, or preview setup | [Project setup and theme](references/setup-and-theme.md) | A migration or uncovered declaration is part of the task |
| Overlay, Window, dialog, sheet, or popup lifecycle | [Overlay and Window catalog](references/overlays-and-windows.md) | An exact signature or version claim remains |
| Preference, dropdown, spinner, or anchored menu family | [Preference and menu guide](references/preferences-and-menus.md) | An exact call or version claim remains |
| Glass surfaces, Glass bars, Glass popups, or backdrop sampling | [Glass components](references/glass.md) | A lower-level material/blur declaration or page-level placement remains |
| Desktop native window, caption bar, or draggable region | [Desktop window guide](references/desktop-window.md) | The page content or platform packaging question remains |
| Navigation routes, back stack, transitions, or gestures | [miuix-nav guide](references/miuix-nav.md) | An exact beta01 declaration or local-source customization is unresolved |
| Color token or component `*Defaults` lookup | [Color semantics and visual lookup](references/color-lookup.md) | The named component is not covered or its source differs |
| Icons, blur, squircle, material, or platform effect implementation | [Styling, icons, and effects](references/styling-icons-and-effects.md) | The effect requires a version-sensitive source check |

### Progressive loading protocol

1. Read the selected primary reference only.
2. Read only the matching heading, table, or contract card.
3. Follow a local link only when the current claim is missing evidence; do not load related references by association.
4. Before writing code, complete the source-first implementation loop. Stop once the required declaration, module/import boundary, host requirement, state/callback contract, and evidence label are known.

## Reference contract

The index is a router, not a second catalog. Once the symbol family is known, read one contract guide only. That guide is expected to contain the declaration, import/module boundary, defaults, minimal call shape, host/state contract, and the version/evidence boundary needed for that family.

Do not add a new contract card because a symbol merely exists. Add or split one only when the API is high-frequency, structurally important, or repeatedly causes integration errors. Keep the maintained declaration in one contract guide; the pinned source remains authoritative.

## Delivery contract

For orientation, report the capability, likely module layer, public family route, and unresolved target questions. For code, include the smallest call, host/state contract, module/import boundary, verification label, and compilation boundary. Do not claim visual or device validation from source inspection alone.
