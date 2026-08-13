# UI Review and Improvement Workflow

Use this workflow when the user asks to improve, polish, modernize, restructure, or fix an existing Miuix UI, especially when code and a screenshot must be reconciled. The goal is a small, evidence-backed improvement that preserves behavior and can be verified. Resolve theme and host ownership with [Project setup and theme](setup-and-theme.md) before changing page boundaries.

## Contents

- [Establish the Target](#establish-the-target)
- [Map the Existing UI](#map-the-existing-ui)
- [Classify the Problem](#classify-the-problem)
- [Gather the Right Evidence](#gather-the-right-evidence)
- [Choose and Implement the Fix](#choose-and-implement-the-fix)
- [Validate the Result](#validate-the-result)
- [Accessibility and Platform Checks](#accessibility-and-platform-checks)
- [Adaptive and Inset Matrix](#adaptive-and-inset-matrix)
- [Minimum Evidence by Risk](#minimum-evidence-by-risk)
- [Report the Outcome](#report-the-outcome)

## Establish the Target

1. Read the user's stated goal and constraints before proposing visual changes.
2. Inspect the current implementation. If a screenshot, mockup, preview, or device capture is available, inspect it together with the code; neither view alone explains both appearance and behavior.
3. Identify the Miuix dependency version used by the target project. This Skill's catalog follows the `0.9.4-rc01` candidate snapshot at `4a6b750b`; verify separately when the project targets another version or the stable `v0.9.3` release.
4. Preserve intentional project conventions unless they cause the reported problem or the user asks to replace them.

Ask a question only when a missing choice would materially change the visual direction. Continue read-only inspection while that choice is unresolved.

## Map the Existing UI

Trace the page from the outside in:

1. Application theme and `ThemeController` inputs.
2. Page host: `Scaffold`, bars, popup host, and supplied content padding.
3. Scroll container, adaptive-width logic, system/IME insets, and navigation layout.
4. Section and surface hierarchy: page → section → Card/container → row/component.
5. State owner and callbacks for selected, enabled, expanded, loading, error, and dismissal states.
6. Custom modifiers, colors, text styles, shapes, effects, and magic dimensions.

Do not change geometry until the actual view hierarchy and coordinate/inset model are understood.

## Classify the Problem

Assign each observed problem to one or more categories before editing:

| Category | Typical evidence | First place to investigate |
|---|---|---|
| Structure and hierarchy | Weak grouping, too many containers, unclear primary action | Closest integrated Example page/section and component choice |
| Component selection | Hand-built control, Material component, wrong Overlay/Window variant | Scenario/component catalogs and matching docs |
| Spacing and shape | Uneven rhythm, clipping, arbitrary radii | Component Defaults, existing project spacing, Example composition |
| Color and typography | Poor contrast, literal colors, inconsistent scale | [Design language and Defaults](design-language.md), theme source, component defaults |
| State and interaction | Wrong state owner, disabled row still clickable, broken dismissal | Demo, integrated Example, and source contract |
| Insets and adaptation | Content under bars, IME overlap, broken wide layout | Scaffold contract, page shell, target platform behavior |
| Accessibility | Missing descriptions, tiny targets, unreadable scaled text | Component semantics, target accessibility requirements, rendered checks |

Prioritize structural and behavioral problems before decorative polish; surface-level tuning cannot repair a wrong host, hierarchy, or state model.

## Gather the Right Evidence

For every change, load the smallest evidence set that answers it:

1. Use the [basic component catalog](component-selection.md), [Overlay/Window catalog](overlays-and-windows.md), or [Preference family guide](preferences-and-menus.md#choose-the-family) to identify the intended public Miuix component.
2. Read its doc for purpose and supported behavior.
3. Read the matching `docs/demo/` file for a minimal call shape.
4. Read the closest `example/shared/` page or section through [Example-derived usage patterns](usage-patterns.md) for grouping, state flow, and integration.
5. Resolve exact paths through [Source verification](source-verification.md), then read the pinned candidate source and Defaults for uncertain parameters, colors, dimensions, host behavior, or edge cases.

Record which finding is a source contract, an Example pattern, an existing project convention, or a user-directed visual choice. Do not present one category as another.

## Choose and Implement the Fix

1. State the intended hierarchy and interaction in plain language.
2. Select the smallest coherent set of changes that solves the highest-impact problems.
3. Prefer the public Miuix component, its Defaults, and semantic theme tokens.
4. Adapt the relevant Example pattern to the target architecture; do not copy Example navigation, blur, diagnostics, or helper infrastructure without need.
5. Preserve state ownership, callbacks, and navigation behavior unless the task explicitly includes behavior changes.
6. Avoid unrelated cleanup. If a broader redesign would materially change scope, explain the tradeoff before expanding it.

Common failed approaches include arbitrary spacing until one screenshot looks right, wrapping every row in a Card, mixing Material tokens into Miuix, ignoring Scaffold padding, and importing an API from a different Miuix release or another navigation library.

## Validate the Result

Validate in layers appropriate to the change:

1. Review the diff for accidental behavior or scope changes.
2. Run format/static checks used by the target project.
3. Compile the smallest affected target or variant.
4. For visual changes, render or inspect a preview, screenshot, emulator, or device at the relevant state.
5. Recheck light/dark or dynamic color, disabled/selected/error states, text scaling, insets, and compact/wide layouts when the change touches them.

Compilation proves API and type compatibility, not visual correctness. If no render or device evidence is available, report visual confirmation as pending rather than inferred.

## Accessibility and Platform Checks

When the change affects interactive controls, text, navigation, or effects, add the checks that apply to the target platforms:

- Preserve component semantics and labels. Give meaningful icons a localized `contentDescription`; keep decorative icons out of the accessibility traversal, and do not replace a component with a raw clickable glyph when the component already owns role/state semantics.
- Check touch and pointer targets, focus order, keyboard activation, hover/press feedback, and the enabled/disabled announcement. Verify custom slots because their semantics and `enabled` state are not inferred automatically.
- Test long localized strings, RTL layout/direction, text scaling or platform dynamic type, and IME/focus behavior. Do not use a screenshot at one font scale or locale as proof that the layout is robust.
- Recheck contrast and state communication in light, dark, and dynamic color modes. Error, selected, disabled, loading, and focus states must remain distinguishable without color alone.
- Make errors and loading states available as visible text and/or semantics when they need to be announced; do not make a tooltip the only channel for an error, status, or required action.
- For dialogs, menus, and popups, verify that focus enters the active surface, keyboard/pointer dismissal works, and focus returns to the trigger when the surface closes. If a custom wrapper changes semantics, inspect its merged and cleared semantics tree rather than assuming the child component's labels survive.
- Validate only the platforms and window classes the target supports, but record unsupported or unverified behavior explicitly. Blur, predictive back, separate windows, pointer input, and adaptive navigation commonly need runtime evidence beyond compilation.

## Adaptive and Inset Matrix

Check the rows that apply to the target instead of treating one device screenshot as proof:

| Condition | Check |
|---|---|
| Compact/wide or resized window | Navigation state remains hoisted and singular; content reflows without duplicating bars or route stacks |
| System bars and Scaffold padding | Content, app bars, floating controls, and scroll indicators consume the owning insets exactly once |
| IME and focused field | The focused field and error/supporting text remain visible; dismissal and back behavior do not lose the draft |
| Orientation, fold, or pane change | Width constraints, popup anchoring, and dialog/window choice remain valid after the host changes size |
| Long strings, large font, or RTL | Text wraps or truncates intentionally, controls keep usable targets, and directional icons/spacing follow layout direction |

## Minimum Evidence by Risk

Match the validation claim to the risk of the requested change:

| Request or risk | Minimum evidence before claiming done |
|---|---|
| API or parameter change | Pinned source/doc evidence and compilation of the affected target |
| Page structure or state-flow change | Compilation plus preview/render, or an explicit statement that visual confirmation is pending |
| Visual polish | Before/after render, screenshot, or preview at the relevant states |
| Insets, dialog/window, back/gesture, blur, or adaptive behavior | Runtime/emulator/device evidence on the supported target platform |
| Version migration | Exact tag/commit diff, affected-target compilation, and a regression checklist for changed APIs |
| Accessibility or responsive behavior | Semantics/interaction inspection plus relevant font-scale, RTL, window-size, and IME checks |

If a minimum evidence item is unavailable, report it as pending rather than upgrading a source or compilation result into a visual or device claim.

## Report the Outcome

Lead with what changed and why it solves the user's problem. Include:

- The page hierarchy or component decisions that changed.
- The review-specific findings: problem category, repair basis, and any Miuix docs, Example file, source contract, or Defaults that supported non-obvious decisions.
- The exact visual/static/device validation completed, plus any unverified visual state or device behavior that remains.
- For common code-delivery facts, follow the single authoritative [Code Delivery Contract](../SKILL.md#code-delivery-contract) instead of repeating its checklist here.

For a review-only request, order findings by user impact and point to exact target files/components. Do not implement unless the request includes a change.
