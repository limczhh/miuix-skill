# UI Review and Improvement Workflow

Use for existing-page diagnosis, polish, or redesign. This file defines investigation and evidence steps; the [mandatory topic rules](rules/design-system.md#required-topic-rules) define conformance. A review-only request produces findings, not code edits unless the user also requests implementation.

## Establish the target

Read the user's goal and constraints, current source/callers, and any available screenshot/render together. Identify the resolved Miuix version/source, platform, and page boundary. Classify Greenfield/Brownfield/Mixed using the entrypoint; an existing undocumented page remains Brownfield.

Record unknown facts rather than substituting an Example app. Ask only when a missing choice changes the visual direction materially, while continuing independent inspection.

## Map and classify

Map theme, route, hosts/slots, primary content, insets/adaptive policy, section structure, state/callbacks, custom visuals, shared wrappers, and actual gesture participants. Use the [ownership worksheet](page-structure.md#ownership-worksheet) when needed.

| Finding concerns | Normative source | Useful investigation guide |
|---|---|---|
| Host, inset, navigation, bar material/scroll | [Page shell](rules/page-shell.md) | Page structure, top bar, navigation |
| Color, typography, geometry, grouping, language, accessibility | [Design system](rules/design-system.md) | Design language |
| Dependencies, editing, search, popup, drag | [Settings and overlays](rules/settings-and-overlays.md) | Content and overlays |
| Persistence, async/lifecycle, lists/icons, feedback, external actions | [State and performance](rules/state-and-performance.md) | Brownfield evolution |
| Multi-part gestures | [Scroll requirements](rules/page-shell.md#scroll-refresh-and-haptics) | Gesture dynamics |
| Exact parameter, host flag, Default, or version | `miuix-components` source contract | Its narrow contract/source-verification route |

Prioritize ownership/behavior defects before decoration. Classify a finding as a profile violation, justified project exception, API mismatch, or unverified visual/runtime observation using the [exception contract](rules/design-system.md#exceptions-and-conformance).

## Gather evidence and implement when requested

1. Read only the rules and guide sections for the classified problem.
2. Verify the concrete API calls in the target through `miuix-components`. An Example can illustrate a composition, but not replace a declaration or define profile defaults.
3. For a code change, follow [Brownfield evolution](brownfield-evolution.md) to choose the changed seam and preserve/replace/add map.
4. Apply one coherent correction and review the diff for unrelated behavior/scope changes.
5. Use the applicable normative validation sections rather than maintaining another UI-requirement checklist here.

## Match claims to evidence

| Change/risk | Evidence needed for the corresponding claim |
|---|---|
| API or parameter compatibility | Target source plus affected-target compilation |
| Page structure/state flow | Compilation and relevant render/interaction inspection, or explicit pending visual evidence |
| Visual polish | Before/after render, screenshot, or preview in the affected states |
| Insets, popup/window, IME, blur, adaptive layout, or gesture | Runtime/emulator/device interaction on supported targets |
| Version migration | Matching source/version diff, affected-target compilation, and relevant regression checks |
| Accessibility or responsive behavior | Semantics/interaction inspection and affected font-scale/RTL/window/IME conditions |
| External consumer application | Consumer/runtime result, separate from the visible toggle and local save |
| Performance improvement | Relevant runtime measurements; source refactor alone is insufficient |

The applicable rules identify what to inspect: [visual/accessibility](rules/design-system.md#accessibility-and-content-resilience), [page-shell](rules/page-shell.md#validation), [settings/overlay](rules/settings-and-overlays.md#validation), and [state/operations](rules/state-and-performance.md#validation). If a check cannot be performed, describe its evidence as pending.

## Report findings or changes

Order review findings by user impact and point to exact target files/components. For implementation, explain the change and relevant rule/exception basis, verification performed, and unresolved evidence. Use the entrypoint's [Code Delivery Contract](../SKILL.md#code-delivery-contract); profile conformance, API verification, compilation, visual/device results, and performance measurements are different claims.
