# Settings and Overlay Rules

Mandatory for dependent controls, forms, preferences, search, popups, dialogs, sheets, and reordering (see [Scope & Exceptions](design-system.md#scope-and-rule-strength)). Verify exact Miuix host flags, overloads, and callbacks via `miuix-components`.

Contents: [hierarchy & toggles](#settings-hierarchy-and-dependent-controls) · [preference contract](#preference-contract) · [inputs & validation](#inputs-and-validation) · [search](#search) · [popup lifetime](#popup-family-and-lifetime) · [dialogs](#dialogs-and-actions) · [sheets & reordering](#sheets-and-reordering) · [validation](#validation)

## Settings Hierarchy and Dependent Controls

- Group logically using `SmallTitle` + `Card` with [declared card gutters](design-system.md#grouped-content-cards). Choose Preference types by interaction semantics (Switch / single-choice Dropdown or Spinner / Checkbox / Slider), not looks alone.
- Keep dependent children **visible but disabled** when parent is off (explain prerequisite in `summary` if needed; preserve saved values); hide (`AnimatedVisibility` wrapping a `Column` for multiple items) only when irrelevant, unsupported, or deliberately collapsible.
- Separate row-navigation taps from trailing-switch toggles (independent callbacks, never double-fire); propagate `enabled` into custom slots; keep `holdDownState` active during popup/drag until release.

## Preference Contract

- Share **1 canonical key, stored type, default, and legal range** across UI, persistence, restore/import, and runtime consumers (never change only Compose state or enforce legality solely in one click handler). Resolve scoped keys in a shared adapter; communicate required app/service restarts rather than implying immediate apply. Observe external changes with lifecycle cleanup.

## Inputs and Validation

- Confirm-based editors use a separate **draft** initialized per open session (cancel/back/outside tap keeps saved data); immediate-apply controls commit via explicit change handlers. Keep retained closing editors' content intact through exit animation, then reinitialize on next open.
- Validate parsed numbers/bounds (empty, partial `-`/`.`, NaN/Infinity, overflow, out-of-range) consistently with storage policy; display error guidance inline/visible (never infer Material error props on Miuix inputs) and block invalid dismiss.
- Single-input dialogs remember `FocusRequester` and request focus + IME after attachment; initialize selection/cursor deliberately once rather than resetting every recomposition.

## Search

- Separate query, expand/collapse state, focus/IME, and results; clear/cancel/back operate on the same owner and stale animation end must not reopen closed search. Restore query & stable expanded state safely; resolve labels per current config.
- Read animated top clearance in layout phase; count matches over the visible filtered set while preserving hidden selections; keep stable keys/updated callbacks in remembered controllers.
- Keep expanded search (`SearchBar` or in-page `GlassSearchOverlay`) outside recorded Glass scenes, composed until collapse completes, with Back/Escape closing the active search state.

## Popup Family and Lifetime

- Choose `Overlay*` (page-scoped under `Scaffold`; check `renderInRootScaffold` in nested Scaffolds) vs `Window*` (standalone/cross-page) by lifetime/host needs, never by the word "dialog" in the component name. Anchored menus align beside their trigger.
- Keep **1 explicit active-popup identity** (`ActiveOverlay?`); use sequential queues only when a flow truly requires it. Keep open popup family/host stable until exit completes (style changes take effect next session).
- Keep controlled `show = false` popups composed until exit/cleanup finishes; guard `onDismissFinished` cleanups (`holdDownState = false`, draft reset) with session tokens so cancelled exits do not clear a reopened session. Ensure non-dismissible flows have explicit cancel/ack paths and focus returns to triggers appropriately. Verify window resize/IME policies directly rather than blindly forcing global `adjustResize`.

## Dialogs and Actions

- Horizontal two-action rows: **Cancel then Confirm**, equal weights, **`20.dp` gap** (RTL-aware; Confirm uses primary TextButton Defaults).
- Standard single-input dialog gap before action row: **`16.dp`**; shared two-field padding editor: **`12.dp`** inter-field gap + **`24.dp`** before actions.
- Bound long text/lists to usable window/IME height keeping action buttons reachable; prevent duplicate busy submits; remote suggestions must never overwrite valid manual/current input; use window-local feedback when page Snackbar is covered; state affected scope before destructive reset/clear and update visible state only after completion.

## Sheets and Reordering

- Bound sheet height to usable window height (accounting once for status/caption/cutout/IME insets); keep header Cancel/Confirm actions visible; allocate drag-list height from remaining viewport (**`420.dp`** default cap for reusable reorder lists, further clamped by viewport). Check sheet `title` header reservation & `insideMargin`/`PaddingValues` contracts via `miuix-components`.
- Separate **draft order** and **selection set** using stable entity keys (`LazyList` + drag controller); preserve ≥1 selection invariants during edits and commit; arbitrate drag handle reorder vs sheet `enableNestedScroll` dismissal; preserve drag haptics & `holdDownState` accessibility.

## Validation

Test parent-off/child-disabled states, independent row vs switch taps, reopen draft reset, invalid numeric input, cancel/back/outside dismissal, short/IME-constrained windows, search cancel/no-match, and reorder + filter continuity; check persistence and external-consumer state separately.
