---
name: miuix-guidelines
description: "Use to design, compose, review or refactor Miuix 0.9.5-beta02 pages and greenfield app shells: ownership, hierarchy, spacing, insets, adaptive navigation, pager-backed destinations, route boundaries, settings/overlays, UI state and scroll/refresh coordination. Includes vague page requests such as '普通页面', '页面太乱', or '几个页面加底部悬浮导航'. Leave isolated API lookups and compile errors to miuix-components."
---

# Miuix UI Guidelines

## Scope

Serve only **Miuix `0.9.5-beta02`**, pinned to `v0.9.5-beta02` / `66248bacc7be29ae6bdc0957de9458b42ac67732`. Do not introduce older-version recipes. Report dependency mismatches; changing dependencies still needs task authorization. Runtime/platform fallbacks remain applicable and Glass is optional.

This skill owns page composition and a chosen design profile. `miuix-components`, when available, resolves selected APIs through pinned official docs, integration notes and source when needed. For work involving both, settle structure here, resolve selected calls there, then check their composition. An isolated API question needs only the component skill.

## Rules and guides

**`references/rules/` is the only source of mandatory UI requirements.** It includes both correctness constraints (host/inset/state ownership, sampling, popup lifecycle, accessibility) and this skill's chosen visual profile (bar treatment, spacing, grouping and adaptive thresholds). The visual choices are prescriptive within scope; they are not universal Miuix library restrictions.

Before implementation or review, read [scope and rule strength](references/rules/design-system.md#scope-and-rule-strength), [exceptions](references/rules/design-system.md#exceptions-and-conformance) and [topic routing](references/rules/design-system.md#required-topic-rules), then only the visual/topic sections touched by the change. User requirements take precedence. A justified exception does not itself require another approval.

Other references are flexible investigation workflows, decisions and placement examples. Adapt them to the existing project; they neither create extra UI requirements nor override rules. A conceptual explanation need not run a conformance review. A guide snippet is not API proof.

## Work at the affected boundary

Inspect the existing page before classifying it:

- **Greenfield:** no reusable page contract, or shell replacement is authorized. Apply relevant rules to the new boundary.
- **Brownfield:** an existing page/helper/host is real even without documentation. Preserve unrelated behavior; mark affected boundaries preserve/replace/add and change only the requested scope.
- **Mixed:** a new feature within an existing shell. Keep the shell Brownfield and apply the profile inside the new boundary.

For a small change, inspect its actual owner/callers and matching rule sections; do not inventory the entire application. For a new page or structural change, map the route, theme/host, primary content/scroll owner, inset handoff, transient layers and state owner. Use the [ownership worksheet](references/page-structure.md#ownership-worksheet) only when that map is unresolved. Unknown context stays unknown rather than becoming an invented host or product requirement.

Resolve semantic content after the boundary: a page label alone does not mandate cards, preferences, a list or navigation chrome. Inspect available code before asking; ask only if an unresolved decision materially changes the structure or behavior.

## Choose one guide

| Task | Primary guide |
|---|---|
| New app shell with several destinations and bottom/floating navigation | [Greenfield app shell](references/greenfield-app-shell.md), then [Greenfield](references/greenfield-page.md) |
| New page / first shell | [Greenfield](references/greenfield-page.md) |
| Existing page, component or shared-helper change | [Brownfield](references/brownfield-evolution.md) |
| Unclear ordinary/settings page or combined layout | [Page structure](references/page-structure.md) |
| Review, polish, “页面太乱” | [UI review](references/ui-review-workflow.md) |
| Grouping, colors, Defaults or system-settings appearance | [Design language](references/design-language.md) |
| Pinned/collapsing/blurred top bar | [Top bar](references/top-bar-compositions.md) |
| Destination bar/rail, tabs/pager selection or floating actions | [Navigation](references/navigation-compositions.md) |
| Scroll, refresh, rebound or pager conflict | [Gesture dynamics](references/gesture-dynamics.md) |
| Settings, forms, search, dialogs, sheets or menus | [Content and overlays](references/content-and-overlays.md) |
| New explicit Glass composition | [Glass composition](references/glass-compositions.md) |
| Replace ordinary components with Glass or keep both | [Glass migration](references/glass-migration.md) |

Start with one guide plus applicable rules; open another only for an unresolved relationship. Do not load Greenfield and Brownfield together by default, or the whole component catalog for one API question.

## Implement and review

Reuse existing route, state, inset and host owners. For a greenfield app shell, complete the [first-pass delivery contract](references/greenfield-app-shell.md#delivery-contract-for-a-first-pass) with real routes, a real shell host, placeholder pages, and one selection owner before filling in page-specific content. For coupled gestures, trace actual modifier connections and the wrapper's callers before changing order or adding a bridge. Apply the chosen composition using the component skill's documentation-first path, escalating only uncertain library facts to source inspection. Avoid repeating API declarations or component tutorials in design guides.

Check the affected rules, behavior continuity and exceptions within the changed scope. Compile the affected target when available and verify the relevant visual/interaction states separately. Stop when the requested result is handled; report unrelated defects without turning them into an unsolicited rewrite.

## Code Delivery Contract

Explain the resulting behavior and non-obvious ownership/inset decisions or profile deviations. A complex change may need the ownership worksheet; a routine conforming edit needs no ceremonial blueprint or full checklist in the response.

Keep rule conformance, API evidence, compilation, rendering/device behavior and performance measurements separate. Maintained integration facts are `catalog-verified`; reading official docs, inspecting target source and compiling are distinct claims. Report what was actually checked and the material remaining uncertainty. User requirements outrank this profile.
