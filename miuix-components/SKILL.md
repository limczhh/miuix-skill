---
name: miuix-components
description: "Use when working in a Miuix project and a task needs factual library knowledge: mapping a vague UI need to a component family, surveying a source area, checking availability, exact public API signatures, imports, modules, defaults, host or lifecycle behavior, version differences, dependency setup, or source verification. Do not trigger for page composition or visual hierarchy when no library contract is needed."
---

# Miuix Components

## Scope

Miuix is an independent Compose Multiplatform UI library with a Xiaomi/HyperOS visual language. This Skill has two related jobs: orient the agent inside the Miuix library, and verify the public contract needed by an implementation. It does not prescribe a page template, information hierarchy, or product-specific visual direction; use `miuix-guidelines` for those decisions when that Skill is available.

The maintained source-audit snapshot is upstream `main` at commit `d8efe1872c08c863a8b33e7044dd2a8b43038032` (`v0.9.4-80-gd8efe187`), which is newer than the latest `v0.9.4` release and is not itself a released version. The historical `v0.9.4` tag remains available only for migration and target-version comparison. If the target project uses a published or different commit, identify the mismatch before presenting version-sensitive code.

## Natural-language intake

The user does not need to know a Miuix symbol. First translate ordinary wording into a capability and a missing discriminator; do not force the user to choose a component from the catalog.

| User wording | First route | Source-backed discriminator; do not assume it yet |
|---|---|---|
| “想要一个搜索框，输入后能展开结果” | If it is page-level, `miuix-guidelines` first; otherwise [Component selection](references/component-selection.md) | `SearchBar` is the expanded container that renders the supplied content and `InputField` is its editable field; verify whether the request needs expanded result content or only text input |
| “底下放几个页面入口/底栏” | `miuix-guidelines` for shell ownership, then [Component selection](references/component-selection.md) | `NavigationBar` and `FloatingNavigationBar` are the navigation family and document 2–5 items; the visual variant and route owner still depend on context |
| “设置项点开可以选几个值” | [Preference and menu guide](references/preferences-and-menus.md) | First distinguish a complete setting row from a standalone control, then plain dropdown items from richer spinner items; Overlay versus Window is a host/lifetime decision |
| “做一个弹窗/下拉菜单” | [Overlay and Window catalog](references/overlays-and-windows.md) | Decide whether it belongs to the current `Scaffold` or an independent window; do not infer the variant from the word “弹窗” alone |
| “顶部大标题跟着滚动收起来” | `miuix-guidelines` for placement, then [Component selection](references/component-selection.md) | `TopAppBar` uses a scroll behavior and the same `MiuixScrollBehavior` must connect to the primary scroll owner through its nested-scroll contract |
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

- Treat the pinned Miuix source as the authority for public signatures, defaults, host behavior, and version contracts.
- Never infer Miuix names or parameters from Material, Material 3, another Compose library, or a similarly named component.
- Keep public API, documentation, Demo, Example, and target-project evidence distinct. Example code demonstrates integration; it is not a public API contract.
- Report source inspection and compilation as separate facts. `source-verified` does not mean `compile-verified`.
- Do not turn a component's existence or default into a requirement that every page use it.

## Evidence levels

Do not promote a lower-level route into a stronger claim:

| Evidence level | It can answer | It cannot answer by itself |
|---|---|---|
| **Verified API contract** | A covered declaration's package, module, parameters, defaults, and basic host/state contract | Whether the target project's dependency and source set compile it |
| **Component catalog** | Which public family or capability matches a semantic role | The complete overload, import block, or version-sensitive behavior |
| **Library map** | Which pinned source area and focused reference to inspect next | That every symbol in that area is public or importable |
| **Docs/Demo/Example** | Intended use, isolated call shape, or integrated composition/state flow | A replacement for the pinned declaration or a universal page rule |
| **Target-project evidence** | The version, dependency, source set, existing host, and compilation result that govern the actual task | A claim about the maintained main-branch snapshot when the target uses another version |

When returning an API fact, name the strongest evidence actually checked. If the answer came only from a map, catalog, or Example, route to source verification before writing a version-sensitive call.

## Source-first implementation loop

When the task asks for Miuix code, the references are a route into evidence, not a substitute for the pinned source.

1. Inspect the target project's Miuix version, platform/source set, dependency declaration, existing theme, and host before choosing a call. If the target project is unavailable, mark those parts as placeholders.
2. Choose one reference route below to locate the component or source area.
3. Acquire the declaration through [Source verification](references/source-verification.md#acquire-source-evidence), then verify the public package, module, overload, parameters, defaults, opt-in, and host/lifecycle behavior required by the task.
4. Use docs and Demo for intended usage, and Example or real consumer code only for integrated composition. Do not promote any of them to API authority.
5. Write the smallest call that matches the verified declaration. Do not fill an uncovered API or import by analogy.
6. Compile the affected target when it is available. Report `source-verified; compilation pending` when compilation was not run.

## Reference routing

Start with exactly one primary reference. A link is a route, not an instruction to preload every reference. Open a second reference only when the task contains a separate unresolved question.

| Task | Primary reference | Open another reference only when |
|---|---|---|
| What is Miuix, what modules or capability layers does it provide, or which dependency should start a task? | [Library overview](references/library-overview.md) | A semantic role or exact symbol still needs to be selected |
| Survey an unfamiliar Miuix module or source area | [Library map](references/library-map.md) | The map routes to a public component, exact call, or source behavior that the task needs |
| Identify a component family or public capability | [Component selection](references/component-selection.md) | The selected symbol needs an exact call or host contract |
| Exact theme or Scaffold call | [Theme and page host contract](references/contracts/theme-and-host.md) | The target version differs or the host contract is incomplete |
| Exact top-bar or scroll behavior call | [Top bars and scroll contract](references/contracts/top-bars-and-scroll.md) | The target version differs or gesture behavior is also involved |
| Exact Card, SmallTitle, or navigation call | [Surface and navigation contract](references/contracts/surfaces-and-navigation.md) | The target version differs or page ownership is also involved |
| Exact dialog/window call | [Overlay and Window contract](references/contracts/overlays.md) | The target version differs or the host/lifecycle contract is incomplete |
| Exact preference, input, or button call | [Input and action contract](references/contracts/inputs-and-actions.md) | The target version differs or the symbol is not covered |
| Find the right contract guide for an exact call | [Verified API contract index](references/api-contracts.md) | The symbol is not listed or the target version differs |
| Exact call for an uncovered API, behavior, or version mismatch | [Source verification](references/source-verification.md) | A component catalog or matching evidence is needed to identify the symbol |
| Dependency, theme, root host, or preview setup | [Project setup and theme](references/setup-and-theme.md) | A migration or uncovered declaration is part of the task |
| Overlay, Window, dialog, sheet, or popup lifecycle | [Overlay and Window catalog](references/overlays-and-windows.md) | An exact signature or version claim remains |
| Preference, dropdown, spinner, or anchored menu family | [Preference and menu guide](references/preferences-and-menus.md) | An exact call or version claim remains |
| Glass surfaces, Glass bars, Glass popups, or backdrop sampling | [Glass components](references/glass.md) | A lower-level material/blur declaration or page-level placement remains |
| Desktop native window, caption bar, or draggable region | [Desktop window guide](references/desktop-window.md) | The page content or platform packaging question remains |
| Navigation routes, back stack, transitions, or gestures | [miuix-nav guide](references/miuix-nav.md) | An exact declaration is missing or the task is a version migration |
| Color token or component `*Defaults` lookup | [Color semantics and visual lookup](references/color-lookup.md) | The named component is not covered or its source differs |
| Icons, blur, squircle, material, or platform effect implementation | [Styling, icons, and effects](references/styling-icons-and-effects.md) | The effect requires a version-sensitive source check |
| `v0.9.3` → `v0.9.4` migration | [Migration notes](references/release-v0.9.4.md) | A changed declaration must be checked in the historical tag |

### Progressive loading protocol

1. Read the selected primary reference only.
2. Read only the matching heading, table, or contract card.
3. Follow a local link only when the current claim is missing evidence; do not load related references by association.
4. Before writing code, complete the source-first implementation loop. Stop loading references once the symbol has a verified declaration, module/import boundary, host requirement, and state/callback contract required by the task.

## Reference contract

The index is a router, not a second catalog. Once the symbol family is known, read one contract guide only. That guide is expected to contain the declaration, import/module boundary, defaults, minimal call shape, host/state contract, and the version/evidence boundary needed for that family.

Do not add a new contract card because a symbol merely exists. Add or split one only when the API is high-frequency, structurally important, or repeatedly causes integration errors. Keep the maintained declaration in one contract guide; the pinned source remains authoritative.

## Handoff boundary

If the task changes page ownership, page skeleton, information hierarchy, grouping, or visual review, hand that concern to `miuix-guidelines` instead of expanding this Skill into a page-design manual. If the task is both page design and implementation, let the guidelines Skill own composition and use this Skill only for the concrete library contracts that the implementation actually calls.

## Delivery contract

For a library-orientation answer, report the interpreted capability, relevant layer, smallest likely module set, public family route, and unresolved version/platform questions. Do not present representative names from the overview as verified imports.

When returning Miuix code, include the smallest integration-ready call, its required ancestor or host, the module/import boundary, and the state or dismissal contract. State whether the declaration was source-verified and whether the affected target compiled. Do not claim visual or device validation from source inspection alone.
