---
name: miuix-guidelines
description: "Use when designing, composing, reviewing, refactoring, or incrementally evolving a Miuix Compose page, including vague requests such as ‘做个普通页面’、‘像系统设置一样’ or ‘页面太乱了’, when the task involves page skeleton, Scaffold/app-bar/navigation composition, ownership, hierarchy, spacing, state placement, insets, adaptive behavior, visual consistency, or scroll/refresh gesture coordination. Do not trigger for a standalone exact Miuix API lookup or compile-error diagnosis unless page composition is also involved."
---

# Miuix UI Guidelines

## Scope

This Skill defines one concrete Miuix design specification derived from the HyperLyric consumer implementation. It governs page structure, ownership, composition, hierarchy, spacing, and review; it is intentionally a chosen design language, not a claim that Miuix has only one valid visual style or that every page must use one fixed component recipe.

Miuix is an independent Compose Multiplatform UI library with a Xiaomi/HyperOS visual language. The companion library's current source-audit snapshot is upstream `main` at commit `d8efe1872c08c863a8b33e7044dd2a8b43038032`; exact signatures, imports, defaults, and version-sensitive behavior belong to `miuix-components`. This Skill codifies the selected HyperLyric-derived usage profile; it does not require the optional Glass family or any one visual variant when the product does not choose it.

## Joint decision boundary

Use the two Skills as a handoff, not as two competing page/component guides:

| Question | Owner | Handoff rule |
|---|---|---|
| Page skeleton, hierarchy, Insets, Scaffold ownership, composition, or gesture ownership | `miuix-guidelines` | Settle the page boundary and component relationship first |
| Concrete component, parameter, import, module, Defaults, host, lifecycle, or version contract | `miuix-components` | Verify only the symbols the chosen composition will call |
| Both page design and implementation are involved | `miuix-guidelines` → `miuix-components` → `miuix-guidelines` | Structure first, API contract second, composition review last |
| Target declaration differs from the maintained catalog | `miuix-components` | Inspect the target's resolved source; a mismatch alone is not `API verification blocked` |

Keep the evidence labels separate: `catalog-verified` describes the maintained pinned snapshot, `target-source-verified` describes the target project's resolved source, and `compile-verified` requires a successful target compilation.

## Design profile contract

Apply the profile in three layers:

1. **Structural baseline:** one application theme, one route owner, one host for each real shell/page boundary, one primary content/scroll owner per page, and one named inset handoff to the final content boundary. Deviate only when the target architecture or product behavior gives a concrete reason.
2. **Visual baseline:** prefer Miuix component defaults, semantic theme roles, meaningful grouping, and the component that owns the interaction semantics. This is the default visual language, not an API requirement.
3. **Profile metrics:** in Greenfield work, use the spacing and hierarchy values in [Design language and Defaults](references/design-language.md) as the default rules of this design specification. In Brownfield work, preserve an established target convention only when it is a real project-level rule rather than an isolated literal. These are profile rules, not hidden Miuix library constants.

The profile fixes responsibilities more strongly than it fixes components. It may require that a bottom-navigation owner live in the shell, while still allowing the implementation to choose `NavigationBar`, `FloatingNavigationBar`, a rail, or no navigation chrome according to the product and available width.

## Natural-language intake

Users normally describe the result they want rather than the page architecture or component name. Translate that wording into a structural question before selecting a recipe. A page being called “settings”, “home”, “list”, or “detail” does not choose its components automatically.

| User wording | First route | Structural question to resolve |
|---|---|---|
| “帮我做个普通页面/设置页” | [Page structure and ownership](references/page-structure.md) | What existing theme, route host, page boundary, primary content owner, and state owner already exist? Classify it as Greenfield, Brownfield, or Mixed after inspection. |
| “从零做一个页面/第一次搭建” | [Greenfield page](references/greenfield-page.md) | Is there truly no existing page contract, and can the profile become the page's starting design system? |
| “已有页面优化/统一间距/改得更像这套规范” | [UI review workflow](references/ui-review-workflow.md), then [Brownfield evolution](references/brownfield-evolution.md) | Which current structure and behavior must survive, and which visual boundaries should migrate toward the profile? |
| “滚动、下拉刷新、回弹、折叠顶栏手势打架” | [Gesture dynamics](references/gesture-dynamics.md) | Which component owns each nested-scroll connection, and which visual state needs an explicit overscroll bridge? |
| “像系统设置一样” | [Page structure and ownership](references/page-structure.md), then [Design language and Defaults](references/design-language.md) | Which grouping and hierarchy are wanted? Do not silently turn this into a mandatory `SmallTitle` + `Card` + Preference template |
| “顶部大标题滚动时收起来” | [Top bar compositions](references/top-bar-compositions.md) | Which page host owns the top bar, which one scrolls, and where does the shared scroll behavior connect? |
| “底下放几个入口/做个底栏” | [Page structure and ownership](references/page-structure.md) | Is it persistent shell navigation or a page action group, and which owner holds the selected destination/back stack? |
| “想做毛玻璃页面/玻璃底栏” | [Glass compositions](references/glass-compositions.md) | Is Glass an explicit visual requirement, where is the recorded `Backdrop` boundary, and which host owns the bars and overlays? Hand exact Glass calls to `miuix-components` |
| “加个弹窗/下拉选择” | [Page structure and ownership](references/page-structure.md), then the component host contract | Does the surface belong to this page `Scaffold` or need an independent window lifetime? |
| “加个搜索框” | [Page structure and ownership](references/page-structure.md) | Does search expand in the page host, own result content, or filter an existing scroll owner? Hand the exact `SearchBar`/`InputField` call to `miuix-components` only after this is known |
| “页面太乱/不像 Miuix” | [UI review workflow](references/ui-review-workflow.md) | Is the defect structural, behavioral, inset-related, or merely visual? Fix ownership and hierarchy before tuning spacing or color |

Do not start with a questionnaire. Inspect the existing page and host when available. If no project context exists, make the smallest reasonable structural assumption, state it, and ask only one question when the answer would change the shell, primary scroll owner, or surface lifetime. Record the user's intent separately from the assumptions made by the Skill.

When project files are available, the existing implementation takes precedence over the user's page label: a request that merely says “做个普通页面” can still be Brownfield or Mixed if the target already has a shell, helper, route, or state contract.

## Workflow modes

- Choose Greenfield, Brownfield, or Mixed first. Add Gesture dynamics, a narrow composition route, Existing-page review, or Isolated visual decision only when the task contains that secondary concern. The operating-mode table below defines how the profile applies in each case.

## Select the operating mode

Classify the task before loading a detailed reference:

Use the modes differently; they are not three names for the same workflow:

| Mode | Starting truth | How this design profile applies | First prohibition |
|---|---|---|---|
| Greenfield | There is no usable page contract, or replacement is explicitly authorized | The profile is the starting design system. Apply its skeleton, `12.dp` grouped-card metrics, page-padding rules, and selected composition recipes before choosing local alternatives | Do not invent a second visual system from habit or copy an unrelated Example page wholesale |
| Brownfield | The existing page, host, state, and behavior are real contracts, even if the page has no documented design system | The profile is the target for the requested visual delta. Audit the current page, preserve working boundaries, and align one coherent scope at a time; absence of documentation does not make the page Greenfield | Do not rebuild the shell or normalize every literal `dp` value merely because the page is visually inconsistent |
| Mixed | An old shell contains a genuinely new page/feature boundary | Keep the shell Brownfield. Apply the profile to the new boundary and connect it through the existing route, inset, state, and host contracts | Do not replace old shell ownership just to make the new feature easier to compose |

The presence of a new Miuix component does not make a task Greenfield. In Brownfield mode, do not run the Greenfield protocol as a reason to rebuild a working page. First trace the existing call chain, name what must remain stable, classify the visual delta, and choose the smallest seam that can host the change.

## Operating contract

1. Classify the task as Greenfield, Brownfield, or Mixed before choosing a page recipe; then resolve the existing application context before writing or changing a page.
2. Establish the page skeleton before choosing components: shell/page host ownership, primary content/scroll owner, inset handoff, and transient layers.
3. Choose components by semantic role and user intent. A settings page, list page, form page, detail page, and custom-content page are all ordinary pages; no page type implies a mandatory component.
4. Apply the declared profile metrics and composition rules in Greenfield work. In Brownfield work, preserve the target project's established design convention only when the existing implementation shows that it is intentional and coherent.
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
5. **Semantics:** choose components by the role they express inside that skeleton. A page type does not select its components automatically.
6. **API handoff:** send only the concrete Miuix symbols the implementation will call to `miuix-components` for source verification.
7. **Composition:** write the smallest page composition that satisfies the skeleton and preserves the target project's behavior.
8. **Validation:** compile the affected target when available, then separately report preview/render/device and accessibility evidence.

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

## Structural invariants

- Keep one application-owned `MiuixTheme` around the UI subtree.
- Keep one owner for the route/back stack rendered by a shell; navigation chrome presents that state but does not create another routing state.
- Keep one primary content/scroll owner per page boundary unless nested scrolling expresses an intentional relationship.
- Consume each host's Scaffold/window/system inset contract through a named shell-to-page handoff and one final page content boundary; do not repeat the same inset on every child.
- Keep Overlay surfaces under their intended page `Scaffold`; use Window surfaces when they must be independent of that page host.
- On a Glass page, keep the recorded backdrop layer and Glass surfaces distinct; for `GlassDialog`/`GlassBottomSheet`/`GlassDropdownPreference`, also keep the `Scaffold` popup host and scrim out of the recorded layer, while `BoxScope` popup components stay beside their anchor in the same root box. Do not let a surface sample itself.
- Keep persistent/business state in the screen owner or model and transient presentation state near the host that renders it.

## Reference routing

Start with exactly one primary reference. Read another only for a distinct question that the primary reference cannot answer.

| Task | Primary reference | Open another reference only when |
|---|---|---|
| Truly new page or shell with no reusable contract | [Greenfield page](references/greenfield-page.md) | The routed structure/composition reference leaves a concrete question |
| Existing page upgrade, component migration, new effect, or shared-page utility change | [Brownfield evolution](references/brownfield-evolution.md) | The change also involves the gesture chain or an exact Miuix symbol |
| Scroll + pull-to-refresh + overscroll + collapsing bar/pager interaction | [Gesture dynamics](references/gesture-dynamics.md) | The page host or migration seam is still unresolved |
| Pinned or collapsing top bar | [Top bar compositions](references/top-bar-compositions.md) | The page host or gesture chain is still unresolved |
| Bottom navigation, adaptive rail, or host-owned floating action | [Navigation and action compositions](references/navigation-compositions.md) | Route ownership or adaptive policy is still unresolved |
| Explicit Glass shell or backdrop-based page | [Glass compositions](references/glass-compositions.md) | The Glass host/backdrop API still needs exact verification in `miuix-components` |
| Grouped content, forms, details, dialogs, sheets, menus, or page-scoped overlays | [Content and overlay compositions](references/content-and-overlays.md) | The page boundary or gesture chain is still unresolved |
| Ambiguous combination of several composition slots | [Page composition routes](references/page-compositions.md) | Select only the narrow guide for the unresolved slot |
| Review-only polish or visual/structural diagnosis of an existing UI | [UI review workflow](references/ui-review-workflow.md) | The diagnosis authorizes a code change, then route to Brownfield |
| Define or apply this Skill's design language: profile metrics, grouping, semantic colors, defaults, loading, or failure presentation | [Design language and Defaults](references/design-language.md) | The page boundary or integrated pattern is unresolved |
| Integrated hierarchy, state flow, or adaptive composition pattern | [Integrated usage patterns](references/usage-patterns.md) | A structural or visual decision is still unspecified |

For exact Miuix API signatures, dependency declarations, component availability, or version claims, hand off only that narrow question to `miuix-components`.

### Progressive loading protocol

1. Read the selected primary reference only. Do not load both Greenfield and Brownfield references by default.
2. Read only the relevant heading or decision table.
3. Follow a local link only when it resolves a concrete ownership, hierarchy, or composition gap.
4. Stop once the page has a mapped skeleton, owner for each state/layer, relevant visual decision, and a validation plan.

## Reference contract

Every narrow composition reference must be read as an executable decision guide, not as a component catalog. Before using it, confirm its `Use when` boundary; while using it, distinguish fixed ownership rules from selectable implementation options; after using it, complete its API handoff and validation boundary. `MUST`/`DO NOT` wording is reserved for ownership, lifecycle, gesture, inset, and source-verification failures that would create a concrete defect.

The profile's declared metrics and combinations are intentionally prescriptive for this Skill. Do not present them as Miuix API requirements, and do not use an isolated Example or consumer detail outside the declared profile as a universal rule.

## Code Delivery Contract

The user's product and behavior requirements outrank these defaults. Treat the guidance as a consistent baseline, not as proof that one component or style is officially mandatory. When code is requested, state the interpreted request, surrounding theme/host assumptions, state ownership, inset boundary, and API verification handoff separately from visual or device validation. Do not describe a composition recipe as compile-verified unless the target project was compiled; report the companion API evidence label separately (`catalog-verified`, `target-source-verified`, or pending/blocked).
