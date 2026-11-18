---
name: miuix-components
description: "Use for Miuix 0.9.5-beta01 component selection, exact APIs, imports, modules, defaults, host/lifecycle contracts, dependency setup, or source investigation. Use miuix-guidelines for page structure and visual composition; this skill handles the concrete library facts."
---

# Miuix Components

## Scope

Support only **Miuix `0.9.5-beta01`**, pinned to `v0.9.5-beta01` / `6d2554a850b8960e2616c968263ff64beb5ffb03`. Use the full beta coordinate. Report another resolved version as outside scope; do not add historical contracts or change dependencies without task authorization.

This skill selects components and verifies their library contracts. Page ownership, hierarchy, spacing and visual composition belong to `miuix-guidelines` when available. For a combined task, settle the page boundary there, verify only the selected APIs here, then review the composition. A library default is not a requirement that every page use it.

## Choose one route

For vague wording, identify the semantic role first: preference row versus standalone control, expanded search versus text input, Overlay versus Window, ordinary blur versus Glass. Inspect the existing project before asking; ask only when an unresolved choice changes the contract. A known symbol can go directly to its section without reading the catalog or contract index first.

| Need | Start here |
|---|---|
| What Miuix provides or which module fits | [Library overview](references/library-overview.md) |
| Choose a component from an ordinary-language request | [Component selection](references/component-selection.md) |
| Locate an unfamiliar source area | [Library map](references/library-map.md) |
| Dependency, theme, host or preview setup | [Setup](references/setup-and-theme.md#quick-start) |
| Exact theme / Scaffold call | [Theme](references/contracts/theme-and-host.md#theme) / [page host](references/contracts/theme-and-host.md#page-host) |
| Top bar / refresh / overscroll / pager | [Top bar](references/contracts/top-bars-and-scroll.md#top-app-bar) / [refresh](references/contracts/scroll-refresh-and-overscroll.md#public-declarations) / [pager](references/contracts/pager-and-breadcrumb.md#pager) |
| Card, section title or navigation | [Surfaces and navigation](references/contracts/surfaces-and-navigation.md) |
| Exact preference / menu / input / action | [Preferences and menus](references/api-contracts.md#preferences-and-menus) / [inputs and actions](references/api-contracts.md#inputs-and-actions) |
| Exact dialog, sheet, popup or Snackbar | [Overlay sections](references/api-contracts.md#overlays) |
| Exact Glass component or modifier | [Glass sections](references/api-contracts.md#glass) |
| Find a specific covered symbol | [Contract index](references/api-contracts.md#select-one-contract-guide) |
| Choose a popup host / preference model / Glass sampling boundary | [Overlay vs Window](references/overlays-and-windows.md#choose-the-host-first) / [preferences](references/preferences-and-menus.md) / [Glass](references/glass.md#background-sampling-contract) |
| Colors / icons, blur or shapes | [Color lookup](references/color-lookup.md) / [effects](references/styling-icons-and-effects.md) |
| Route stack / native desktop window | [miuix-nav](references/miuix-nav.md) / [desktop window](references/desktop-window.md) |
| Uncovered API, source override or uncertain behavior | [Source verification](references/source-verification.md#acquire-source-evidence) |

Read the selected section, not every linked file. Follow another route only for a separate unresolved question. For long contracts, use their section links; do not preload the whole family.

## Implement with the right evidence

1. **Confirm context once.** Inspect the declared/resolved beta01 coordinate, substitutions or local changes, source set, and relevant host. Reuse this finding until build/source inputs change. If the project is unavailable, use the pinned baseline and leave target resolution pending.
2. **Use the covered-contract path.** When the relevant API is covered and the target uses standard beta01 without a conflicting override, use its maintained contract. Check required arguments, state/callback direction and host for the call being written. Do not fetch the same source again solely to upgrade an evidence label.
3. **Investigate only the gap.** Read pinned source for uncovered symbols, conflicting/customized source, ambiguous overloads or behavior not answered by the contract. Follow the [source gate](references/source-verification.md#api-verification-gate); library declarations control API facts, while Demo/Example only explain usage or integration.
4. Write the smallest matching call and compile the affected target when available. Never fill a missing Miuix parameter/import by analogy with Material or another component.

Keep contracts exact but selective: public declarations, required inputs, meaningful defaults, state/host boundaries and integration pitfalls. Label an intentional excerpt as partial; do not present a guessed or merged overload as an exact signature. Add coverage for demonstrated needs, not merely because another symbol exists.

## Delivery contract

For orientation, give the smallest capability/module map that answers the request. For code, include the call and any non-obvious dependency, host or state requirement. Avoid a repeated investigation transcript.

Use `catalog-verified` when reusing the pinned contract, `target-source-verified` only after inspecting the target declaration, `compile-verified` only after a successful target compilation, and `runtime-verified` only for exercised behavior. See [evidence boundaries](references/source-verification.md#verification-labels) when needed. State missing target/compile evidence; never infer visual or device success from a matching signature. Another version is a scope mismatch, not automatically `API verification blocked`.
