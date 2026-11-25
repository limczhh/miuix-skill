# Page Workflows, Decision Order, and UI Review

Consolidated workflow guide for choosing design roles, mapping page ownership, starting Greenfield single pages, evolving Brownfield seams, and auditing UI quality. Enforce rules from [rules/](rules/design-system.md); verify concrete APIs via `miuix-components`.

Contents: [design decision order](#design-decision-order) · [page boundary & ownership](#page-boundary-and-ownership-worksheet) · [greenfield single page](#greenfield-single-page-workflow) · [brownfield evolution](#brownfield-evolution-workflow) · [UI review & evidence](#ui-review-workflow)

## Design Decision Order

1. **Map page boundary** (Greenfield / Brownfield / Mixed, host, route, inset, state owners).
2. **Load applicable rules** via [Design system topic table](rules/design-system.md#required-topic-rules).
3. **Choose interaction role before component/token**:
   - Canvas (`background`) vs grouped containment (`surfaceContainer` / `Card`) vs primary/selected/destructive intent vs supporting summary vs divider vs untinted multicolor art.
   - Establish text hierarchy (title / section label / row title / summary / body / tag) from content before picking sizes.
   - Standard blurred chrome follows [Page shell rules](rules/page-shell.md#blur-and-glass); request Glass explicitly via [Glass adoption](glass-adoption.md).
4. **Verify concrete symbols, Defaults & version contracts via `miuix-components`**.

## Page Boundary and Ownership Worksheet

```text
application theme & root route owner
└── shell: persistent navigation + global popup layers
    └── page boundary (host: Scaffold or direct content root)
        ├── top bar / header
        ├── primary content / vertical scroll owner
        ├── floating action / toolbar / snackbar slot
        └── page-scoped Overlay (or separate Window layer)
```

Fill this concise worksheet **only for non-trivial structural redesigns or unfamiliar shells** (leave unknowns explicitly marked until inspected):

```text
Existing theme & root host:
Route / back-stack owner:
Navigation chrome & adaptive policy owner:
Page entry, host & shared helpers/callers:
Primary content & vertical scroll owner:
Shell-to-page inset handoff & single consumer:
Transient surface family (Overlay vs Window) & feedback host:
Persistent state, draft & presentation owners:
```

Translate user phrasing first:
- *"Ordinary / settings page"* -> inspect shell/content before picking `SmallTitle` + `Card` + `*Preference` vs form/list.
- *"Multi-page app with bottom/floating bar"* -> start with [App shell & navigation](app-shell-and-navigation.md).
- *"Large collapsing title" / "Render behind status bar"* -> [Layout patterns](layout-patterns.md#top-bar-and-status-bar-composition).
- *"Scroll/refresh/pager conflict"* -> [Layout patterns](layout-patterns.md#gesture-dynamics-diagnosis).
- *"Dialog / sheet / menu / search"* -> [Layout patterns](layout-patterns.md#content-and-overlay-composition).

## Greenfield Single-Page Workflow

1. Confirm existing theme, route, platform, source set, and Miuix version before declaring the boundary new (for multi-tab shells, do [App shell & navigation](app-shell-and-navigation.md) first).
2. Map route, host, content/scroll, inset, and state ownership using the worksheet if non-trivial.
3. Load matching rules ([Page shell](rules/page-shell.md) -> standard adaptive `TopAppBar` for scrollable pages; add [Settings/Overlays](rules/settings-and-overlays.md) / [State/Perf](rules/state-and-performance.md) as needed).
4. Select the placement pattern ([Layout patterns](layout-patterns.md) or [Glass adoption](glass-adoption.md)), verify exact APIs with `miuix-components`, implement, compile when available, and report separate evidence.

## Brownfield Evolution Workflow

Use when routes, hosts, shared page helpers, or gesture wrappers already exist. Keep edits minimal and scoped.

1. **Inspect outside-in along the real call chain** (check wrapper callers too — e.g., an ordinary list branch vs a `PullToRefresh` branch can invert who holds the scroll connection):
   `route/back stack → shell/nav → page host → inset handoff → content modifier chain → popups/blur consumers → state/lifecycle owners`.
2. **Define delta decisions** (`preserve / replace / add` per boundary: Route/state, Host/insets, Content/gesture, Visual profile, New behavior). Missing documentation does not justify rewriting working unrelated code.
3. **Implement at the right seam**: reuse working wrapper slots where they already own padding/behavior; migrate one representative caller and check compilation before expanding to siblings; keep dual renderers sharing one state owner.
4. **Verify & close**: validate rule conformance inside touched scope (`preserve` checks too), remove temporary compat code only when obsolete, and report unrelated defects separately.

## UI Review Workflow

Use for page polish, *"页面太乱"*, or structural audits (produce findings only unless code edits are also requested).

1. **Establish target**: read goal, current source/callers, and any screenshots together; classify Greenfield / Brownfield / Mixed (never substitute an Example app for missing target code).
2. **Map & prioritize defects by impact**:
   1. Broken ownership (route/back, double scroll connections, missing inset consumption, wrong popup lifetime, unsafe IO in composition).
   2. Rule violations vs justified project conventions ([Exception criteria](rules/design-system.md#exceptions-and-conformance)).
   3. API mismatches / unsupported assumptions (handoff to `miuix-components`).
   4. Visual grouping, spacing (`12.dp` / `16.dp`), hierarchy, typography, accessibility, responsive sizing.
3. **Match claims strictly to obtained evidence**:

| Claim | Required evidence |
|---|---|
| API / parameter compatibility | Pinned/target Kotlin source + compilation |
| Layout structure / state flow | Compilation + render/interaction check (else mark visual pending) |
| Visual polish / blur / dark & light | Rendered preview or device screenshot across affected states |
| Insets / IME / Window / adaptive / gestures | Interactive runtime/emulator test on supported platforms |
| Accessibility / RTL / font scale / responsive | Semantics + large-text/RTL/narrow/IME inspection |
| Performance improvement | Actual runtime measurements (source refactor alone is insufficient) |
