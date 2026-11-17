# Brownfield Evolution

Use this reference when an existing Compose/Miuix project already has a route, page Scaffold, shared page utility, CompositionLocal, state flow, overlay policy, or gesture chain. The goal is to add or migrate behavior with the smallest coherent change while preserving the existing product contract.

This is not a weaker version of the Greenfield rules. It has a different starting point: the current implementation is evidence and a compatibility boundary. A page can have no documented design system and still be Brownfield; existing code, behavior, and ownership do not become disposable merely because the visual rules were never written down. A new Miuix component, a new visual effect, or a new API version does not by itself justify rebuilding the page skeleton.

## Entry rule

Before editing, record:

- the project Miuix version and the source commit or published artifact actually used;
- any Gradle includeBuild or dependency-substitution rule that overrides the version catalog; the resolved source checkout is the API authority in that case;
- the route/back-stack owner and the page entry;
- the existing theme and page/shell hosts;
- the current inset and content-padding handoff;
- the primary scroll owner and every nested-scroll, overscroll, pager, or refresh participant;
- shared helpers, providers, feature flags, and variant selectors;
- persistent state, transient presentation state, and lifecycle callbacks;
- the smallest behavior the user wants changed.

If a fact is not visible in the code or build configuration, mark it unknown. Do not replace an unknown with the Example application's structure.

## Trace the existing call chain

Trace from the outside in and keep the names from the target project:

    route/back stack
      -> shell and navigation chrome
        -> page entry and page host
          -> content-padding/inset handoff
            -> primary content owner
              -> modifier and nested-scroll chain
                -> visual consumers and transient surfaces
                  -> state/provider/lifecycle owners

Search callers, not only the declaration of a shared helper. A PageUtils-like function may be a real compatibility boundary because many pages depend on its modifier order, state object, or feature switch.

When a target already has a page wrapper with this shape, treat it as an integration seam rather than replacing it:

- the wrapper creates one Scaffold, one MiuixScrollBehavior, and the page's shared backdrop/list state;
- the top bar receives that behavior;
- custom page content receives the already-computed PaddingValues and the same ScrollBehavior;
- the default list branch owns the existing page-scroll modifier, while the optional PullToRefresh branch wraps the list without feeding the top-bar behavior a second time.

This is a verified consumer pattern, not a Miuix-required API. Preserve the callable boundary and inspect its callers before changing the internals.

## Use the profile as a migration target

For an existing page that lacks a documented design system, do not choose between “leave it unchanged” and “rebuild it as Greenfield.” Use the current page as the compatibility baseline and the design profile as the visual target:

1. Inventory the current page's section structure, component combinations, spacing literals, content padding, host ownership, and state/gesture behavior.
2. Classify each visual decision as already aligned with the profile, an intentional product choice, an accidental inconsistency, or unknown.
3. Select one coherent migration scope: a section group, one page shell, one navigation family, or one shared helper. Do not normalize unrelated pages in the same pass.
4. Apply the profile's declared rules inside that scope. For example, convert a grouped section to `SmallTitle` followed by a Card with `12.dp` horizontal inset and `12.dp` bottom gap, while preserving the page's route, state, and scroll owner.
5. Validate the changed scope against neighboring pages before expanding the migration. If the user requests full-page alignment, make the scope explicit and migrate in ordered passes rather than silently replacing infrastructure.

The absence of a spacing token file is not permission to invent new values, and it is not evidence that every existing value is wrong. The profile supplies the target rule; the current implementation determines the safe seam and the necessary migration size.

## Preserve, replace, add

Write this table before a non-trivial change:

| Boundary | Current owner or behavior | Decision | Reason/evidence |
|---|---|---|---|
| Route and back stack | existing file/type | preserve | not part of the requested change |
| Page host and inset handoff | existing Scaffold/helper | preserve or replace | concrete defect or new host requirement |
| Primary scroll owner | existing list/form/pager | preserve or replace | interaction requirement |
| Gesture chain | existing connections and callbacks | preserve, adapt, or replace | source and runtime evidence |
| Visual metrics and hierarchy | existing spacing, grouping, Defaults, and custom decoration | preserve, align, or replace | profile rule, user target, or demonstrated visual defect |
| State | screen/model/provider | preserve | prevent state bifurcation |
| New behavior | none or named seam | add | requested result |

The first pass should change only rows marked replace or add. If the map shows a real ownership defect, document that defect and its impact before moving the boundary.

## Evolution procedure

1. **Classify the delta.** Decide whether it is presentation-only, host/inset, component/API migration, interaction/dynamics, or cross-page infrastructure. A task can have more than one category, but name the primary one.
2. **Map the current chain.** Identify the actual route, host, content owner, modifier order, state, providers, and lifecycle. Do not infer the chain from a component name.
3. **Select the smallest seam.**
   - Keep a change page-local when one page has the behavior.
   - Put repeated page semantics in a shared helper when the call sites truly share the same contract.
   - Use an application adapter when old and new implementations must coexist.
   - Use a CompositionLocal only for tree-wide cross-cutting context that several descendant branches need.
4. **Define a semantic contract.** The caller should ask for a product behavior such as page scroll integration, a selected destination, or a popup action. It should not know which Miuix version, Glass branch, or platform capability implements that behavior.
5. **Implement one complete path.** Preserve the existing route, state, insets, accessibility semantics, and lifecycle. Do not scatter a half-migrated flag across every row or modifier.
6. **Add dynamic coexistence only when required.** Resolve the capability and user preference at one boundary, then choose one implementation for that page/session. Both variants must consume the same semantic state and callbacks.
7. **Migrate call sites incrementally.** Move one representative page first, compile it, and compare behavior before changing the remaining pages. Keep the old path only while an actual supported version, device, rollout, or product switch needs it.
8. **Validate the old and new paths.** Check route restoration, insets, state continuity, dismissal, gesture handoff, compact/wide layout, and the product's requested visual state. Remove the adapter or fallback when its migration condition no longer exists.

## Dual implementation and fallback

Use a shared application-owned policy rather than conditional logic spread through page code:

    capability + user preference + rollout policy
        -> one resolved page presentation mode
            -> shared semantic state and callbacks
                -> classic or advanced implementation

The policy must distinguish:

- a capability fallback, such as an API/platform limitation;
- a product preference, such as a user-selected classic mode;
- a rollout decision, such as a temporary staged migration.

Do not let a capability check silently override an explicit user choice without explaining the product rule. Do not keep two independent refresh, selection, navigation, or draft states just because two renderers exist. The adapter owns selection; the screen/model owns the meaning.

An adapter is justified when it removes repeated version checks or stabilizes a public application call shape. It is not justified as a wrapper around one call site that adds no policy or lifecycle value.

A useful presentation adapter exposes one semantic page-bar or dialog call and selects the Glass or classic implementation inside that boundary. The page continues to pass title, actions, shared ScrollBehavior, backdrop, insets, and dismissal callbacks without knowing which branch is active. Keep the branch at the wrapper boundary; do not scatter the mode check through every page.

## CompositionLocal as global context

CompositionLocal is appropriate for a small tree-wide capability that multiple descendant branches need and that is naturally scoped to the current page/application subtree, for example:

- a backdrop sampler or visual context;
- a popup action translator;
- a gesture coordinator or page motion context;
- a resolved presentation capability.

For each local, define the provider boundary, required/default behavior, lifetime, and whether absence is valid. Keep ordinary page data, events, selected items, and business state as explicit parameters or screen/model state. Do not use a CompositionLocal to hide a one-level data flow problem or to create mutable global state.

When a provider is introduced during migration, first show that at least two independent descendant branches need the same capability. Otherwise keep the value at the nearest common owner.

## Gesture and overlay boundaries

If the evolution touches scrolling, refresh, overscroll, collapsing bars, or pager gestures, stop here and read [Gesture dynamics](gesture-dynamics.md). Existing modifier order and state bridges are part of the behavior contract; do not replace them with the ordinary collapsing-bar snippet.

If the evolution touches a dialog, sheet, menu, or popup:

- preserve the current host family unless the lifecycle requirement changes;
- keep show/active-overlay state controlled by the host;
- let the component perform its exit animation; do not remove it from composition immediately in a way that cuts off the exit path;
- put cleanup that must wait for the exit animation in the component's finished callback;
- for input surfaces, verify the target window's IME/inset policy and any inside-margin compensation against the actual component declaration and manifest. On Android, explicitly inspect android:windowSoftInputMode="adjustResize"; keep or add it only when the target window and supported platform require that resize behavior.

## Stop conditions against over-refactoring

Stop expanding the change when:

- the requested behavior works at an existing seam;
- the route, state, inset, and host contracts are preserved;
- a proposed rewrite is justified only by stylistic preference;
- a new abstraction has only one caller and no version or lifecycle policy;
- a fallback has no concrete supported platform, version, rollout, or user-choice reason;
- the remaining uncertainty is an exact Miuix API question that belongs to miuix-components.

Report any discovered structural defect separately from the requested migration. Do not silently turn a component upgrade into a shell rewrite.

## Evidence boundary

Separate the basis of each decision:

- Miuix source/docs: exact public API, host behavior, defaults, and version facts;
- target-project source: existing ownership, helper contracts, state flow, and compatibility constraints;
- integrated Example: a composition possibility, never a mandate;
- runtime/device evidence: gesture, animation, IME, blur, and adaptive behavior;
- user/product choice: the desired visual or migration policy.

The target project wins where it has an intentional convention and the requested change does not invalidate it. Exact Miuix calls still go through [miuix-components](../../miuix-components/SKILL.md).
