# Brownfield Evolution Workflow

Use when the target already has routes, hosts, page helpers, state, providers, or gesture behavior. This file describes inspection, migration scope, and validation order. The [design-system rules](rules/design-system.md) remain the normative source inside the changed scope; a new component/effect does not turn an existing page into Greenfield.

## Inspect the actual chain

Record the resolved Miuix artifact/source, platform/source set, route entry, theme, hosts, padding handoff, primary scroll owner, gesture participants, shared helpers/callers, presentation switches, state owners, and the requested delta. Account for `includeBuild` or dependency substitution rather than trusting a version catalog alone.

Trace outside-in:

```text
route/back stack → shell/navigation → page host
→ inset handoff → primary content and modifier chain
→ visual/popup consumers → state/provider/lifecycle owners
```

Search the callers of a helper as well as its declaration. A wrapper may assign the app-bar connection to the ordinary list but to PullToRefresh in a refresh branch. Use [Gesture dynamics](gesture-dynamics.md) to inspect that configuration; the helper's name is not topology evidence.

A working wrapper is a likely integration seam. For example, a page-specific custom-content slot may already receive effective padding and the same scroll behavior as its bar. Map that seam before introducing another host/provider.

## Define the migration scope

1. Inventory the affected structure, metrics, controls, state, and interactions.
2. Classify each relevant decision as aligned, an intentional project convention, an accidental inconsistency, or unknown using the [exception criteria](rules/design-system.md#exceptions-and-conformance).
3. Select one coherent scope: a section, page wrapper, navigation family, or shared helper with known callers.
4. Map boundaries with this delta table:

| Boundary | Current implementation | Decision | Reason/evidence |
|---|---|---|---|
| Route/state | actual owner | preserve/replace/add | requested change or proven defect |
| Host/insets | actual helper/handoff | preserve/replace/add | affected contract |
| Content/gesture | actual scroll owner/chain | preserve/replace/add | selected integration |
| Visual profile | actual component/metrics | preserve/replace/add | applicable rule or justified exception |
| New behavior | actual seam or absent | preserve/replace/add | requested result |

Use replace/add for boundaries that will change in the first pass. Record a real owner defect before moving that boundary. Missing design documentation is not evidence that all existing code is disposable.

## Implement at the selected seam

- Classify the primary delta: presentation, host/inset, API migration, gesture, data/state, or cross-page infrastructure.
- Select the page-local change or shared wrapper that fits actual callers. [Component rules](rules/design-system.md#component-selection-and-defaults) own when a wrapper is justified; [state rules](rules/state-and-performance.md#state-ownership-and-lifecycle) own provider and renderer-state boundaries.
- Settle the semantic call shape: title/actions/content, selected destination, popup operation, or refresh action. Resolve version/material details at the appropriate adapter boundary.
- Apply the matching topic rules and verify concrete APIs through `miuix-components` before writing target calls.
- Migrate one representative caller first, compile/check it, then expand only to the requested related callers.
- If two renderers must coexist, map the capability/preference/rollout decision and the shared state owner using the [variant rule](rules/state-and-performance.md#state-ownership-and-lifecycle).

For popup changes, inspect the existing family and target window behavior against [popup lifetime](rules/settings-and-overlays.md#popup-family-and-lifetime). For blur/bar changes, compare the existing wrapper with [standard TopBar](rules/page-shell.md#standard-top-app-bar) and its justified Brownfield exceptions.

## Close the migration

Check the changed route/state continuity, padding, dismissal, gesture handoff, compact/side layout, and relevant runtime states through the applicable rule file's validation section. Remove temporary compatibility only when its supported condition no longer exists.

Stop expanding when the requested behavior is handled at the selected seam, or the remaining uncertainty is an exact API question. Report unrelated defects separately. Use the [Code Delivery Contract](../SKILL.md#code-delivery-contract) and distinguish target source, compilation, visual/device, and external-consumer evidence.
