---
name: miuix-guidelines
description: "Use to design, compose, review, or refactor Miuix pages and greenfield app shells: ownership, hierarchy, spacing, insets, adaptive navigation, pager-backed destinations, route boundaries, settings/overlays, UI state, and scroll/refresh coordination (including '普通页面', '页面太乱', '加底部悬浮导航'). Leave exact API lookups, imports, and version contracts to miuix-components."
---

# Miuix UI Guidelines

## Purpose and Scope

This skill captures the chosen **Miuix page-architecture conventions, ownership boundaries, spacing metrics, and mandatory UI rules**.
- Exact Miuix APIs, module/package boundaries, parameter Defaults, and release details (`0.9.5-rc01`) belong exclusively to `miuix-components`; do not duplicate or maintain an API manual here.
- Keep **`references/rules/`** distinct from workflow/pattern guides: `rules/` contains mandatory requirements (`MUST` / `DO NOT` / normative metrics); guides explain workflows and adaptable placement examples (`user requirements` always outrank this profile).

## Work at the Affected Boundary

Inspect existing code before classifying:
- **Greenfield**: No reusable host/shell contract (or shell replacement requested) -> apply all applicable rules to the new boundary.
- **Brownfield**: Existing helper/host is the contract -> preserve unrelated code, mark touched boundaries `preserve / replace / add`, change only the requested scope.
- **Mixed**: New feature inside an existing shell -> keep the shell Brownfield and apply rules inside the new boundary.

Resolve semantic content after ownership: a screen named "Settings" does not automatically force cards, preferences, or chrome without real justification.

## Direct Routes

Read [scope, exceptions & topic routing](references/rules/design-system.md#required-topic-rules) plus the relevant guide or rule file:

| Need / Task | Open directly |
|---|---|
| Design rules: scope/exceptions, Defaults, colors, typography, shapes, card spacing, icons/tags, i18n, a11y | [Design system rules](references/rules/design-system.md) |
| Shell & nav rules: hosts, greenfield invariants, top bars, routes/back, adaptive layout, tabs/pagers, insets, scroll, blur/Glass | [Page shell rules](references/rules/page-shell.md) |
| Settings & overlay rules: hierarchy, preference contract, validation, search, popup lifetime, dialogs, sheets/reordering | [Settings & overlay rules](references/rules/settings-and-overlays.md) |
| State & ops rules: ownership, persistence, async, lazy lists/icons, feedback/Snackbar, motion, permissions, import/export | [State & performance rules](references/rules/state-and-performance.md) |
| Decision order, ownership worksheet, Greenfield single page, Brownfield evolution, UI review & audit | [Workflows](references/workflows.md) |
| Multi-destination app shell (`NavDisplay` + `HorizontalPager` + floating/bottom bar + `SelectionCoordinator`) | [App shell & navigation](references/app-shell-and-navigation.md) |
| Top bar status-bar extension, scroll/refresh/overscroll diagnosis, grouped cards & mutually exclusive popups | [Layout patterns](references/layout-patterns.md) |
| Optional Glass adoption, backdrop recording hierarchy, ordinary -> Glass migration & dual-theme coexistence | [Glass adoption](references/glass-adoption.md) |

## Implementation and Code Delivery

1. Reuse existing theme, route, state, inset, and host owners before introducing new wrappers; build greenfield multi-page shells with real typed routes, placeholder sibling pages, and one selection coordinator before adding per-page polish.
2. Verify all concrete Miuix symbols, overloads, Defaults, and version-specific flags via `miuix-components` before emitting target code.
3. Keep conformance, API verification, compilation, rendered visuals, and performance separate in reports; state unverified runtime/visual checks cleanly rather than assuming compilation proves rendering.
