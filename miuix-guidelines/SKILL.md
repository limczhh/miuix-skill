---
name: miuix-guidelines
description: "Use when designing, composing, reviewing, or refactoring a Miuix Compose page and the task involves page skeleton, Scaffold/app-bar/navigation composition, ownership, hierarchy, spacing, state placement, insets, adaptive behavior, or visual consistency. Do not trigger for a standalone exact Miuix API lookup or compile-error diagnosis unless page composition is also involved."
---

# Miuix UI Guidelines

## Scope

This Skill defines one practical Miuix usage profile. It governs page structure, ownership, composition, hierarchy, and review; it is a consistent baseline, not a claim that Miuix has only one valid visual style or that every page must use one fixed component recipe.

Miuix is an independent Compose Multiplatform UI library with a Xiaomi/HyperOS visual language. The maintained companion library evidence is Miuix `v0.9.4`, pinned to commit `39c40f99`. Exact signatures, imports, defaults, and version-sensitive behavior belong to `miuix-components`.

## Design profile contract

Apply the profile in three layers:

1. **Structural baseline:** one application theme, one route owner, one host for each real shell/page boundary, one primary content/scroll owner per page, and one named inset handoff to the final content boundary. Deviate only when the target architecture or product behavior gives a concrete reason.
2. **Visual baseline:** prefer Miuix component defaults, semantic theme roles, meaningful grouping, and the component that owns the interaction semantics. This is the default visual language, not an API requirement.
3. **Fallback values:** use the spacing and hierarchy values in [Design language and Defaults](references/design-language.md) only when the target project has no established convention. They are Skill-level composition defaults, not hidden Miuix constants.

The profile fixes responsibilities more strongly than it fixes components. It may require that a bottom-navigation owner live in the shell, while still allowing the implementation to choose `NavigationBar`, `FloatingNavigationBar`, a rail, or no navigation chrome according to the product and available width.

## Workflow modes

- **New page or shell:** map the existing context and page skeleton before choosing a visual recipe.
- **Known composition:** use a page composition recipe only after the relevant owners and inset handoff are named.
- **Existing-page review:** map the current implementation, classify the defect, and make the smallest change supported by the evidence.
- **Isolated visual decision:** read only the design-language section that answers the typography, color, grouping, state, or fallback-spacing question; do not invent a page skeleton for a local styling request.

## Operating contract

1. Resolve the existing application context before writing a page: theme, route host, page boundary, state owner, and platform/source set.
2. Establish the page skeleton before choosing components: shell/page host ownership, primary content/scroll owner, inset handoff, and transient layers.
3. Choose components by semantic role and user intent. A settings page, list page, form page, detail page, and custom-content page are all ordinary pages; no page type implies a mandatory component.
4. Preserve the target project's behavior, accessibility, adaptive layout, and state ownership while applying the visual guidance.
5. If implementation needs an exact Miuix symbol, ask `miuix-components` for that narrow contract; do not preload its entire component catalog.
6. Treat every code block in a composition reference as a placement shape, not API proof. Before implementation, verify each Miuix symbol through the companion Skill's pinned source-first workflow.

## Page-first delivery protocol

For any new page, shell refactor, or page-level UI change, complete these decisions in order:

1. **Context:** identify the existing theme, route host, page boundary, platform/source set, and target project's relevant constraints.
2. **Skeleton:** name the route owner, navigation-chrome owner, shell/page host boundaries, primary content/scroll owner, inset handoff, host slots, transient layer, and persistent state owner.
3. **Profile:** apply the visual baseline after the structural boundary is known: preserve target conventions, then use Miuix defaults and semantic roles, then use the fallback values only if needed.
4. **Semantics:** choose components by the role they express inside that skeleton. A page type does not select its components automatically.
5. **API handoff:** send only the concrete Miuix symbols the implementation will call to `miuix-components` for source verification.
6. **Composition:** write the smallest page composition that satisfies the skeleton and preserves the target project's behavior.
7. **Validation:** compile the affected target when available, then separately report preview/render/device and accessibility evidence.

Use this compact record before page code when the task is non-trivial:

```text
Existing theme/host:    file or caller
Route owner:            file, type, or none
Page boundary:          route/entry/composable
Primary content owner:  LazyColumn / Column / Pager / other
Inset handoff:          shell handoff, then final page content consumer
Host slots/layers:      top / bottom / floating / snackbar / Overlay / Window
Persistent state owner: screen owner, ViewModel, or application model
Visual profile choices: existing convention / Miuix defaults / fallback values
```

If a context item is unavailable, mark it as unknown or pending inspection; do not invent a host, route, inset policy, or target-project convention. For a standalone API question with no page composition, stop this protocol and use `miuix-components` directly.

## Structural invariants

- Keep one application-owned `MiuixTheme` around the UI subtree.
- Keep one owner for the route/back stack rendered by a shell; navigation chrome presents that state but does not create another routing state.
- Keep one primary content/scroll owner per page boundary unless nested scrolling expresses an intentional relationship.
- Consume each host's Scaffold/window/system inset contract through a named shell-to-page handoff and one final page content boundary; do not repeat the same inset on every child.
- Keep Overlay surfaces under their intended page `Scaffold`; use Window surfaces when they must be independent of that page host.
- Keep persistent/business state in the screen owner or model and transient presentation state near the host that renders it.

## Reference routing

Start with exactly one primary reference. Read another only for a distinct question that the primary reference cannot answer.

| Task | Primary reference | Open another reference only when |
|---|---|---|
| New page or redesign where ownership, skeleton, scrolling, insets, or transient layers are not yet mapped | [Page structure and ownership](references/page-structure.md) | A visual hierarchy or integrated composition question remains |
| Known shell combination such as Scaffold + app bar/navigation/floating content | [Page composition recipes](references/page-compositions.md) | Ownership or visual hierarchy is still unresolved |
| Existing UI review, polish, or restructure | [UI review workflow](references/ui-review-workflow.md) | The classified issue needs one specific design or composition section |
| Define or apply this Skill's baseline visual language: typography, grouping, semantic colors, defaults, loading, or failure presentation | [Design language and Defaults](references/design-language.md) | The page boundary or integrated Example pattern is unresolved |
| Integrated hierarchy, state flow, or adaptive composition evidence | [Example-derived usage patterns](references/usage-patterns.md) | A structural or visual decision is still unspecified |

For exact Miuix API signatures, dependency declarations, component availability, or version claims, hand off only that narrow question to `miuix-components`.

### Progressive loading protocol

1. Read the selected primary reference only.
2. Read only the relevant heading or decision table.
3. Follow a local link only when it resolves a concrete ownership, hierarchy, or composition gap.
4. Stop once the page has a mapped skeleton, owner for each state/layer, relevant visual decision, and a validation plan.

## Code Delivery Contract

The user's product and behavior requirements outrank these defaults. Treat the guidance as a consistent baseline, not as proof that one component or style is officially mandatory. When code is requested, state the surrounding theme/host assumptions, state ownership, inset boundary, and API verification handoff separately from visual or device validation. Do not describe a composition recipe as compile-verified unless the target project was compiled.
