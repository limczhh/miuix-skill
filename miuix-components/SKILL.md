---
name: miuix-components
description: "Use for Miuix 0.9.5-beta03 component selection and implementation: read version-pinned official docs, resolve API/import/module questions, apply integration notes, and investigate source when needed. Use miuix-guidelines for page structure and visual composition."
---

# Miuix Components

## Scope

Support only **Miuix `0.9.5-beta03`**, pinned to `v0.9.5-beta03` / `3526cfe2ca0779c5a2eab99af8737a96c9a8a885`. Use the full beta coordinate. Report another resolved version as outside scope; do not add historical contracts or change dependencies without task authorization.

This skill resolves component APIs and integration. Page composition belongs to `miuix-guidelines`; combined work goes from page structure to selected APIs, then back to composition.

## Documentation first

Actively read the selected component's official Markdown at the pinned commit; do not merely send the user a link. The [component map](references/component-selection.md#component-reference-table) gives repository paths. Resolve `docs/...` paths as `https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/<path>`, or read them from a matching local checkout with `git show`. Rolling rendered docs and `main` may describe another version.

Official docs own introductions, imports, complete parameters and ordinary examples. Local notes retain host/lifecycle, insets, input and fallback distinctions. Read only the selected doc and relevant note; reuse evidence for unchanged inputs. Escalate gaps, contradictions and target overrides through the [documentation-first workflow](references/source-verification.md#documentation-first-path).

## Choose one route

For vague requests, identify the interaction and existing owner before choosing a component. Ask only when an unresolved choice changes the implementation. A known doc path skips the routers.

| Need | Start here |
|---|---|
| What Miuix provides or which module fits | [Library overview](references/library-overview.md) |
| Choose a component from an ordinary-language request | [Component selection](references/component-selection.md) |
| Dependency, theme, host or preview setup | [Setup](references/setup-and-theme.md#quick-start) |
| Beta02 → beta03 upgrade delta | [Migration note](references/beta03-migration.md) |
| Theme / Scaffold integration | [Theme](references/contracts/theme-and-host.md#theme) / [page host](references/contracts/theme-and-host.md#page-host) |
| Top bar / refresh / tabs and pager | [Top bar](references/contracts/top-bars-and-scroll.md#top-app-bar) / [refresh](references/contracts/scroll-refresh-and-overscroll.md#public-declarations) / [tabs](references/contracts/pager-and-breadcrumb.md#ordinary-tab-rows) |
| Card, section title or navigation | [Surfaces and navigation](references/contracts/surfaces-and-navigation.md) |
| Preference / menu / input / action docs and pitfalls | [Preferences and menus](references/api-contracts.md#preferences-and-menus) / [inputs and actions](references/api-contracts.md#inputs-and-actions) |
| Dialog, sheet, popup or Snackbar | [Overlay notes](references/api-contracts.md#overlays) |
| Glass component or modifier | [Glass notes](references/api-contracts.md#glass) |
| Popup host / preference model | [Overlay vs Window](references/overlays-and-windows.md#choose-the-host-first) / [preferences](references/preferences-and-menus.md) |
| Blur setting, component fallback or inherited sampling | [Blur behavior](references/blur-behavior.md#component-behavior) |
| Colors / icons, blur or shapes | [Color lookup](references/color-lookup.md) / [effects](references/styling-icons-and-effects.md) |
| Route stack / native desktop window | [miuix-nav](references/miuix-nav.md) / [desktop window](references/desktop-window.md) |
| Uncovered API, source override or uncertain behavior | [Source verification](references/source-verification.md#acquire-source-evidence) |

Use the [documentation index](references/api-contracts.md) only when the needed note is unknown. Follow another route only for a separate unresolved question.

## Implement with the right evidence

Confirm the target's resolved beta03 coordinate, source set, overrides and host once. Without a target, use the pinned baseline and report resolution pending. Read the selected docs/notes, then inspect only uncertain declarations or implementation branches. Kotlin declarations resolve API conflicts; Demo/Example explain integration.

Write the smallest matching call and compile the affected target when available. Do not infer Material-style parameters or merge overloads. Maintain links and integration distinctions rather than copied declarations/manuals; label any necessary declaration excerpt as partial.

## Delivery contract

Deliver the implementation or answer with non-obvious dependency, host and state decisions. Do not substitute a documentation link list for requested work.

Use `catalog-verified` for maintained pinned integration facts; name the pinned doc when relying on a fresh documentation lookup. `target-source-verified`, `compile-verified` and `runtime-verified` require their actual checks. See [evidence boundaries](references/source-verification.md#verification-labels). State missing target/compile evidence; a matching API does not prove rendering or device behavior.
