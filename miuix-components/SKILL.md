---
name: miuix-components
description: "Use when a task needs factual Miuix library knowledge: surveying a source area, component availability, exact public API signatures, imports, modules, defaults, host or lifecycle behavior, version differences, dependency setup, or source verification. Do not trigger for page composition or visual hierarchy when no library contract is needed."
---

# Miuix Components

## Scope

Miuix is an independent Compose Multiplatform UI library with a Xiaomi/HyperOS visual language. This Skill answers what the library provides and how its public contracts work. It does not prescribe a page template, information hierarchy, or product-specific visual direction; use `miuix-guidelines` for those decisions when that Skill is available.

The maintained evidence is Miuix `v0.9.4`, pinned to commit `39c40f99`. If the target project uses another version, identify the mismatch before presenting version-sensitive code.

## Operating contract

- Treat the pinned Miuix source as the authority for public signatures, defaults, host behavior, and version contracts.
- Never infer Miuix names or parameters from Material, Material 3, another Compose library, or a similarly named component.
- Keep public API, documentation, Demo, Example, and target-project evidence distinct. Example code demonstrates integration; it is not a public API contract.
- Report source inspection and compilation as separate facts. `source-verified` does not mean `compile-verified`.
- Do not turn a component's existence or default into a requirement that every page use it.

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
| Survey an unfamiliar Miuix module or source area | [Library map](references/library-map.md) | The map routes to a public component, exact call, or source behavior that the task needs |
| Identify a component family or public capability | [Component selection](references/component-selection.md) | The selected symbol needs an exact call or host contract |
| Exact call for a covered API | [Verified API contracts](references/api-contracts.md) | The target version differs or the contract is incomplete |
| Exact call for an uncovered API, behavior, or version mismatch | [Source verification](references/source-verification.md) | A component catalog or matching evidence is needed to identify the symbol |
| Dependency, theme, root host, or preview setup | [Project setup and theme](references/setup-and-theme.md) | A migration or uncovered declaration is part of the task |
| Overlay, Window, dialog, sheet, or popup lifecycle | [Overlay and Window catalog](references/overlays-and-windows.md) | An exact signature or version claim remains |
| Preference, dropdown, spinner, or anchored menu family | [Preference and menu guide](references/preferences-and-menus.md) | An exact call or version claim remains |
| Navigation routes, back stack, transitions, or gestures | [miuix-nav guide](references/miuix-nav.md) | An exact declaration is missing or the task is a version migration |
| Color token or component `*Defaults` lookup | [Color semantics and visual lookup](references/color-lookup.md) | The named component is not covered or its source differs |
| Icons, blur, squircle, shader, or platform effect implementation | [Styling, icons, and effects](references/styling-icons-and-effects.md) | The effect requires a version-sensitive source check |
| `v0.9.3` → `v0.9.4` migration | [Migration notes](references/release-v0.9.4.md) | A changed declaration must be checked in pinned source |

### Progressive loading protocol

1. Read the selected primary reference only.
2. Read only the matching heading, table, or contract card.
3. Follow a local link only when the current claim is missing evidence; do not load related references by association.
4. Before writing code, complete the source-first implementation loop. Stop loading references once the symbol has a verified declaration, module/import boundary, host requirement, and state/callback contract required by the task.

## Handoff boundary

If the task changes page ownership, page skeleton, information hierarchy, grouping, or visual review, hand that concern to `miuix-guidelines` instead of expanding this Skill into a page-design manual. If the task is both page design and implementation, let the guidelines Skill own composition and use this Skill only for the concrete library contracts that the implementation actually calls.

## Delivery contract

When returning Miuix code, include the smallest integration-ready call, its required ancestor or host, the module/import boundary, and the state or dismissal contract. State whether the declaration was source-verified and whether the affected target compiled. Do not claim visual or device validation from source inspection alone.
