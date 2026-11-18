---
name: miuix-guidelines
description: "Use when designing, composing, reviewing, refactoring, or incrementally evolving a Miuix 0.9.5-beta01 Compose page, including vague requests such as ‘做个普通页面’、‘像系统设置一样’ or ‘页面太乱了’, when the task involves page skeleton, Scaffold/app-bar/navigation composition, ownership, hierarchy, spacing, settings interactions, UI state/loading/feedback, insets, adaptive behavior, visual consistency, or scroll/refresh gesture coordination. Do not trigger for a standalone exact Miuix API lookup or compile-error diagnosis unless page composition is also involved."
---

# Miuix UI Guidelines

## Scope

This Skill defines a Miuix design specification. It governs page structure, ownership, composition, visual hierarchy, settings and popup interactions, UI-facing state/data flow, feedback, and review; it is intentionally a chosen design language, not a claim that Miuix has only one valid visual style or that every page must use one fixed component recipe.

Miuix is an independent Compose Multiplatform UI library with a Xiaomi/HyperOS visual language. This Skill serves only `0.9.5-beta01`, pinned to release tag `v0.9.5-beta01` at commit `6d2554a850b8960e2616c968263ff64beb5ffb03`; exact signatures, imports, defaults, and version-sensitive behavior belong to `miuix-components`. Standard pages use the shared blurred adaptive bar: collapsing large title in compact windows, fixed compact title in wide windows, and opaque fallback when blur is inactive. Do not retain older Miuix API branches or introduce another version's recipe. Report a dependency mismatch and apply this baseline only when upgrading is authorized. Runtime/platform capability fallback remains required; the Glass family is optional.

## Joint decision boundary

Use the two Skills as a handoff, not as two competing page/component guides:

| Question | Owner | Handoff rule |
|---|---|---|
| Page skeleton, hierarchy, Insets, Scaffold ownership, composition, or gesture ownership | `miuix-guidelines` | Settle the page boundary and component relationship first |
| Concrete component, parameter, import, module, Defaults, host, lifecycle, or version contract | `miuix-components` | Verify only the symbols the chosen composition will call |
| Both page design and implementation are involved | `miuix-guidelines` → `miuix-components` → `miuix-guidelines` | Structure first, API contract second, composition review last |
| Target resolution differs from beta01 | `miuix-components` | Report the unsupported version or local override; do not add an older-version composition branch |

Keep the evidence labels separate: `catalog-verified` describes the maintained pinned snapshot, `target-source-verified` describes the target project's resolved source, and `compile-verified` requires a successful target compilation.

## Guidance and mandatory rules

This Skill acts as a design specification, and explicitly distinguishes between **Mandatory Rules (刚性强制规范)** and **Non-mandatory Guides (弹性参考实践与工作流)**:

| Category | Location | Purpose & Force |
|---|---|---|
| **Mandatory Design Rules** | `references/rules/*.md`<br>([design-system.md](references/rules/design-system.md), [page-shell.md](references/rules/page-shell.md), [settings-and-overlays.md](references/rules/settings-and-overlays.md), [state-and-performance.md](references/rules/state-and-performance.md)) | **Strict UI conformance requirements**. Governs essential correctness baselines: Insets safe-area handoff, Glass self-sampling exclusion, `HorizontalPager` with `targetPage`, popup exit animation preservation, and accessibility. Must be strictly enforced during implementation and review. |
| **Non-Mandatory Guides & Workflows** | `references/*.md`<br>(e.g. [page-structure.md](references/page-structure.md), [greenfield-page.md](references/greenfield-page.md), [brownfield-evolution.md](references/brownfield-evolution.md), [content-and-overlays.md](references/content-and-overlays.md), [glass-migration.md](references/glass-migration.md), [gesture-dynamics.md](references/gesture-dynamics.md), [ui-review-workflow.md](references/ui-review-workflow.md)) | **Flexible best practices, architectural templates, and migration advice**. These provide patterns and inspiration for how to investigate and structure work. They should be adapted to the target's practical needs and must not be treated as rigid dogma. |

Before implementing or reviewing a page, MUST read the shared scope, exceptions, and topic table in [Design system rules](references/rules/design-system.md), then the visual and topic sections applicable to the changed boundary. Read those rules alongside the selected workflow guide; choosing one primary guide does not waive topic routing. Do not preload unrelated rule sections. Pure conceptual explanations can use the relevant guide without a conformance review. Exact API/version facts live in `miuix-components`.

## Natural-language intake

Users normally describe the result they want rather than the page architecture or component name. Translate that wording into a structural question before selecting a recipe. A page being called “settings”, “home”, “list”, or “detail” does not choose its components automatically.

| User wording | First route | Structural question to resolve |
|---|---|---|
| “帮我做个普通页面/设置页” | [Page structure and ownership](references/page-structure.md) | What existing theme, route host, page boundary, primary content owner, and state owner already exist? Classify it as Greenfield, Brownfield, or Mixed after inspection. |
| “从零做一个页面/第一次搭建” | [Greenfield page](references/greenfield-page.md) | Is there truly no existing page contract, and can the profile become the page's starting design system? |
| “已有页面优化/统一间距/改得更像这套规范” | [UI review workflow](references/ui-review-workflow.md), then [Brownfield evolution](references/brownfield-evolution.md) | Which current structure and behavior must survive, and which visual boundaries should migrate toward the profile? |
| “滚动、下拉刷新、回弹、折叠顶栏手势打架” | [Gesture dynamics](references/gesture-dynamics.md) | Which component owns each nested-scroll connection, and which visual state needs an explicit overscroll bridge? |
| “像系统设置一样” | [Page structure and ownership](references/page-structure.md), then [Design language and Defaults](references/design-language.md) | Which semantic groups and preference interactions fill the standard page shell? |
| “顶部大标题滚动时收起来” | [Top bar compositions](references/top-bar-compositions.md) | Which page host owns the top bar, which one scrolls, and where does the shared scroll behavior connect? |
| “底下放几个入口/做个底栏” | [Page structure and ownership](references/page-structure.md) | Is it persistent shell navigation or a page action group, and which owner holds the selected destination/back stack? |
| “顶栏要模糊/底栏要毛玻璃” | [Top bar compositions](references/top-bar-compositions.md) or [Navigation](references/navigation-compositions.md) | Which shared ordinary-blur wrapper, recorded content layer, runtime/preference policy, and fallback does the bar use? |
| “明确使用 Glass 组件/Glass 弹层” | [Glass compositions](references/glass-compositions.md) | Which Glass family and recorded backdrop boundary fit the host/lifetime? Hand exact Glass calls to `miuix-components` |
| “加个弹窗/下拉选择” | [Page structure and ownership](references/page-structure.md), then the component host contract | Does the surface belong to this page `Scaffold` or need an independent window lifetime? |
| “加个搜索框” | [Page structure and ownership](references/page-structure.md) | Does search expand in the page host, own result content, or filter an existing scroll owner? Hand the exact `SearchBar`/`InputField` call to `miuix-components` only after this is known |
| “设置联动不对/弹窗编辑后怎么保存” | [Brownfield evolution](references/brownfield-evolution.md), with the applicable [settings rules](references/rules/settings-and-overlays.md) | Which value is a draft, which owner persists it, and which dependency/commit contract must remain valid? |
| “页面加载卡顿/刷新失败没有提示/导出结果不对” | [UI review workflow](references/ui-review-workflow.md), with the applicable [state and operation rules](references/rules/state-and-performance.md) | Is the defect in entry cost, request identity, data snapshot, feedback visibility, or the external operation's actual outcome? |
| “页面太乱/不像 Miuix” | [UI review workflow](references/ui-review-workflow.md) | Is the defect structural, behavioral, inset-related, or merely visual? Fix ownership and hierarchy before tuning spacing or color |

Do not start with a questionnaire. Inspect the existing page and host when available. If no project context exists, make the smallest reasonable structural assumption, state it, and ask only one question when the answer would change the shell, primary scroll owner, or surface lifetime. Record the user's intent separately from the assumptions made by the Skill.

When project files are available, the existing implementation takes precedence over the user's page label: a request that merely says “做个普通页面” can still be Brownfield or Mixed if the target already has a shell, helper, route, or state contract.

## Select the operating mode

Classify the task before loading a detailed reference:

Use the modes differently; they are not three names for the same workflow:

| Mode | Starting truth | How this design profile applies | First prohibition |
|---|---|---|---|
| Greenfield | There is no usable page contract, or replacement is explicitly authorized | The profile is the starting design system. Apply the normative design system and selected composition recipes before choosing local alternatives | Do not invent a second visual system from habit or copy an unrelated Example page wholesale |
| Brownfield | The existing page, host, state, and behavior are real contracts, even if the page has no documented design system | The profile is the target for the requested visual delta. Audit the current page, preserve working boundaries, and align one coherent scope at a time; absence of documentation does not make the page Greenfield | Do not rebuild the shell or normalize every literal `dp` value merely because the page is visually inconsistent |
| Mixed | An old shell contains a genuinely new page/feature boundary | Keep the shell Brownfield. Apply the profile to the new boundary and connect it through the existing route, inset, state, and host contracts | Do not replace old shell ownership just to make the new feature easier to compose |

The presence of a new Miuix component does not make a task Greenfield. In Brownfield mode, do not run the Greenfield protocol as a reason to rebuild a working page. First trace the existing call chain, name what must remain stable, classify the visual delta, and choose the smallest seam that can host the change.

## Operating contract

1. Classify the task as Greenfield, Brownfield, or Mixed before choosing a page recipe; then resolve the existing application context before writing or changing a page.
2. Establish the page skeleton before choosing components: shell/page host ownership, primary content/scroll owner, inset handoff, and transient layers.
3. Within the applicable standard page shell, choose body components by semantic role and user intent. A page label alone does not decide whether content is a preference, form, entity list, or custom drawing.
4. Apply the visual requirements, declared profile metrics, and composition rules in Greenfield work and the selected Brownfield migration scope. Preserve an established target convention only with the evidence required by the design-system rules.
5. Preserve the target project's behavior, accessibility, adaptive layout, and state ownership while applying the visual guidance.
6. If implementation needs an exact Miuix symbol, ask `miuix-components` for that narrow contract; do not preload its entire component catalog.
7. Treat every code block in a composition reference as a placement shape, not API proof. Before implementation, verify each Miuix symbol through the companion Skill's source-first workflow.
8. When multiple gesture participants are present, inspect their actual nested-scroll contracts and version before changing modifier order or adding a bridge. Do not assume that a component with a ScrollBehavior parameter only observes state.

## Page-first delivery protocol

For a Greenfield page, shell, or explicitly authorized shell replacement, complete these decisions in order. For a Brownfield change, inspect the existing map first and apply only the decisions needed by the changed boundary:

1. **Intent:** restate the requested result in plain language and list only the assumptions that are not supplied by the user.
2. **Context:** identify the existing theme, route host, page boundary, platform/source set, and target project's relevant constraints.
3. **Skeleton:** name the route owner, navigation-chrome owner, shell/page host boundaries, primary content/scroll owner, inset handoff, host slots, transient layer, and persistent state owner.
4. **Profile:** apply the visual baseline after the structural boundary is known: use this profile's declared metrics and combinations by default, preserve an established Brownfield convention when it is intentional, then use Miuix component Defaults and semantic roles for component-owned details.
5. **Semantics:** choose body components by the role they express inside the selected standard shell or justified variant.
6. **API handoff:** send only the concrete Miuix symbols the implementation will call to `miuix-components` for source verification.
7. **Composition:** write the smallest page composition that satisfies the skeleton and preserves the target project's behavior.
8. **Validation:** check applicable profile rules and justify deviations; compile the affected target when available, then separately report preview/render/device and accessibility evidence.

Use this compact record before page code when the task is non-trivial:

```text
Interpreted request:      plain-language result and explicit assumptions
Existing theme/host:    file or caller
Route owner:            file, type, or none
Page boundary:          route/entry/composable
Primary content owner:  LazyColumn / Column / Pager / other
Inset handoff:          shell handoff, then final page content consumer
Host slots/layers:      top / bottom / floating / snackbar / Overlay / Window
Persistent state owner: screen owner, ViewModel, or application model
Visual profile choices: profile metrics / existing convention / Miuix component Defaults
```

If a context item is unavailable, mark it as unknown or pending inspection; do not invent a host, route, inset policy, or target-project convention. For a standalone API question with no page composition, stop this protocol and use `miuix-components` directly.

For Brownfield work, this record is a delta record rather than a replacement blueprint: fill in the existing owners first, then mark each boundary as preserve, replace, or add. Only the boundaries marked replace or add may change in the first implementation pass.

## Reference routing

Choose one primary workflow or decision guide below. The mandatory [design-system rules](references/rules/design-system.md) are read alongside it for implementation and review. Add another guide only for a distinct unresolved question.

| Task | Primary reference | Open another reference only when |
|---|---|---|
| Truly new page or shell with no reusable contract | [Greenfield page](references/greenfield-page.md) | The routed structure/composition reference leaves a concrete question |
| Existing page upgrade, component migration, new effect, or shared-page utility change | [Brownfield evolution](references/brownfield-evolution.md) | The change also involves the gesture chain or an exact Miuix symbol |
| Scroll + pull-to-refresh + overscroll + collapsing bar/pager interaction | [Gesture dynamics](references/gesture-dynamics.md) | The page host or migration seam is still unresolved |
| Pinned or collapsing top bar | [Top bar compositions](references/top-bar-compositions.md) | The page host or gesture chain is still unresolved |
| Bottom navigation, adaptive rail, or host-owned floating action | [Navigation and action compositions](references/navigation-compositions.md) | Route ownership or adaptive policy is still unresolved |
| Replace ordinary components with Glass, adapt only a surface, or keep both families | [Glass migration and coexistence](references/glass-migration.md) | Exact beta01 APIs or dependency setup still need `miuix-components` |
| Explicit Glass shell or backdrop-based page | [Glass compositions](references/glass-compositions.md) | The Glass host/backdrop API still needs exact verification in `miuix-components` |
| Grouped content, forms, details, dialogs, sheets, menus, or page-scoped overlays | [Content and overlay compositions](references/content-and-overlays.md) | The page boundary or gesture chain is still unresolved |
| Ambiguous combination of several composition slots | [Page structure and ownership](references/page-structure.md) | Use its decision table to select the narrow guide for the unresolved slot |
| Review-only polish or visual/structural diagnosis of an existing UI | [UI review workflow](references/ui-review-workflow.md) | The diagnosis authorizes a code change, then route to Brownfield |
| Define or apply this Skill's design language: profile metrics, grouping, semantic colors, defaults, loading, or failure presentation | [Design language and Defaults](references/design-language.md) | The page boundary or integrated pattern is unresolved |

For exact Miuix API signatures, dependency declarations, component availability, or version claims, hand off only that narrow question to `miuix-components`.

### Progressive loading protocol

1. Choose the primary guide; do not load both Greenfield and Brownfield by default.
2. For implementation or review, read the design-system scope, exceptions, and required-topic table; follow the matching topic rules and visual sections for the changed boundary.
3. In the chosen guide, read only the relevant heading or decision table; follow another guide link only for an unresolved decision.
4. Stop loading once the applicable rules, mapped skeleton, state/layer owners, visual choices, and validation plan are known.

## Reference contract

Every narrow composition reference must be read as an executable decision guide, not as a component catalog. Before using it, confirm its `Use when` boundary; while using it, distinguish requirements from selectable implementation options; after using it, complete its API handoff and validation boundary. The [design-system rules](references/rules/design-system.md) own shared normative requirements. Guides explain how to apply them; code snippets illustrate placement and are not independent definitions of metrics or exceptions. Keep rule changes in the normative file and update affected examples and links.

The profile's declared metrics and combinations are intentionally prescriptive for this Skill. Do not present them as Miuix API requirements, and do not use an isolated Example or consumer detail outside the declared profile as a universal rule.

## Code Delivery Contract

The user's product and behavior requirements outrank this profile. Enforce its applicable requirements within the changed scope without presenting them as official library mandates. When code is requested, state the interpreted request, surrounding theme/host assumptions, state ownership, inset boundary, and API verification handoff separately from visual or device validation. Report non-obvious justified deviations and unresolved profile requirements; a routine conforming change needs no ceremonial checklist. Do not describe a composition recipe as compile-verified unless the target project was compiled; report the companion API evidence label separately (`catalog-verified`, `target-source-verified`, or pending/blocked).
