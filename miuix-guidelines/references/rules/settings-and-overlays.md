# Settings and Overlay Rules

Mandatory for preferences, dependent controls, forms, search, selection, dialogs, menus, sheets, and reordering. Use the [shared scope and exception contract](design-system.md#scope-and-rule-strength). Exact component availability, parameters, popup hosts, and dismissal callbacks must be verified for the target version.

Contents: [settings](#settings-hierarchy-and-dependent-controls) · [persistence](#preference-contract) · [inputs](#inputs-and-validation) · [search](#search) · [popup lifetime](#popup-family-and-lifetime) · [dialogs](#dialogs-and-actions) · [sheets](#sheets-and-reordering) · [validation](#validation).

## Settings hierarchy and dependent controls

- Group by the user's concept and decision order. Use a section title and shared Card for related preferences; use the [declared group metrics](design-system.md#grouped-content-cards), not local per-row tuning.
- Use complete Preference components for a setting row. A Boolean toggle uses a switch preference; a finite mutually exclusive value uses a dropdown/single-choice flow; independent choices use checkboxes; a numeric range uses a suitable slider or input. Do not choose by visual similarity alone.
- Keep child settings visible but disabled when a parent is off by default. Explain the prerequisite in the summary where necessary. Hide only when the content is irrelevant/unsupported, unusually large, or deliberately collapsible; changing a parent does not silently erase child values.
- A row that opens a detail page and a trailing switch that enables the feature are distinct actions. Use a wrapper with explicit independent callbacks; a switch tap must not also navigate or toggle twice.
- Propagate `enabled` through the row, custom actions, and embedded control. Disabled styling alone must not leave a nested click handler active.
- Use component `holdDownState` where supported to keep a popup trigger or actively dragged row visually pressed. Release it on the matching dismissal/drag completion path.
- Multiple children inside `AnimatedVisibility` need an intentional layout container such as `Column`; do not place them at overlapping coordinates. This is not a reason to collapse every dependency.
- Conditional option groups, such as dynamic-theme color choices, may animate appearing/disappearing when they become relevant. This differs from an ordinary parent-disabled child setting: preserve the stored choice and avoid converting visibility into a reset operation.

## Preference contract

- Every setting has one canonical key/identity, stored type, default, legal range/options, and owner. Share those definitions between UI reads, writes, restore/migration, and the runtime consumer.
- The control value and its callback form one controlled-state contract. Persist through the existing preference/repository adapter; changing only Compose state does not complete a configuration operation.
- Keep mutually exclusive and dependent settings legal on initialization, ordinary edits, restore/import, migration, and external updates. Do not enforce the relationship only in one row's click handler.
- A scoped settings screen resolves the canonical storage/consumer key in its adapter. Do not duplicate scope-specific conditions in every row or write the local value under a different remote key.
- A restart/reload-required setting must communicate that requirement. Do not imply that changing a preference proves the external consumer has already applied it.
- Observe relevant external preference changes when they can occur while the page is alive; dispose listeners with the owner. Do not keep an independent per-page configuration database.

See [State and performance](state-and-performance.md#persistence-and-external-consumers) for async synchronization and consumer evidence.

## Inputs and validation

- For confirm-based editors, keep the editing draft separate from the saved value. Initialize it for the current item/open session; edits commit only through the defined confirmation path. Cancel/back/outside dismissal preserves saved data. An immediate-apply control uses its explicit change callback and persistence policy instead of inventing a confirmation step.
- Use appropriate keyboard type and IME behavior. Validate the parsed value as well as characters: empty, incomplete minus/decimal input, overflow, non-finite floats, and out-of-range values need an explicit policy.
- Keep default/range/type normalization in the shared preference policy. Do not accept one range in the dialog and silently store another interpretation during restore or runtime reads.
- Validation failure leaves the draft available and explains the problem beside the field or in a visible feedback surface. A keyboard type is not validation; do not dismiss as though an invalid commit succeeded.
- In a single-input dialog, attach a remembered `FocusRequester` to the editable field and request focus after it is attached, using the window/component lifecycle. Show IME only when appropriate; do not request it during composition or focus every field in a multi-input form.
- For text that should be edited as one value, initialize selection deliberately, such as placing the cursor at the end. Do not reset text/cursor on every recomposition or external callback.
- A retained confirm-based editor reinitializes its draft from the latest saved value on each new open session. Keeping the component alive for exit animation must not make a cancelled draft reappear on the next open. Preserve the closing session's content until exit completes rather than clearing it as soon as `show` becomes false.
- Fields keep semantic labels and supporting/error text. Do not invent Material error parameters on a Miuix input; verify its actual error-color/state API.

## Search

- Separate query, expansion/collapse transition, focus/IME, and result state. Search cancel/clear/back update the same owner; a stale animation completion must not reopen or overwrite a newer search state.
- Restore the query and stable expanded/collapsed intent across supported configuration restoration using a saver or the existing screen owner. Normalize an interrupted expanding/collapsing transition to its intended stable state; recompute results and resolve localized labels from the current configuration rather than persisting stale display text.
- Cancel/back close search through the same current-state action and apply the declared query-clearing policy, including during expansion. A restored expanded search requests focus after attachment without treating an old one-time focus flag as still valid.
- Read animated top clearance in the layout phase when it only changes placement. Bound measurement by the current window constraints; do not invalidate the entire page through a value-form padding read each frame merely to move the search row.
- Keep one query as the filtering source of truth. Compute results from the current source collection plus query/filter/sort inputs, and key remembered transformations on those inputs.
- Distinguish empty query, no matches, loading, failure, and populated results. Keep localized labels current after configuration changes; do not freeze a search label in an unkeyed remembered state.
- The expanded search surface owns its result scroll/focus/input boundary. Coordinate its top offset with the page/system inset and its expanded app-bar presentation; do not leave an invisible overlay intercepting input after collapse.
- Filtering changes presentation, not saved membership or order. If a selected/total count describes visible results, both numbers use that same visible set. Hidden selections remain saved unless the user explicitly changes them.
- Stable long-lived callbacks consume current query/state/actions through proper keys or `rememberUpdatedState`. Do not recreate a remembered gesture/search controller merely because a callback lambda changed identity.

## Popup family and lifetime

- Choose page-hosted Overlay versus separate Window by host/lifetime requirements and target support. A component name containing “dialog” does not decide the family. Anchor menus beside their trigger; do not emulate host behavior with an arbitrary z-index stack.
- When adding Glass, preserve the existing host when the new `OverlayDialog`/`OverlayBottomSheet` typed surface slot suffices. Use the matching dialog/sheet helper rather than a generic popup surface. Full Glass dialog/sheet wrappers use an Overlay host; moving an existing Window flow there requires an explicit host migration and focus/back/IME review.
- Ordinary/Glass coexistence keeps the chosen popup family/host stable for its active session through dismissal completion. A presentation setting change must not recreate an open editor, lose its draft or cut off its exit; apply the new choice on the next session or finish closing before switching.
- Keep visibility/active-popup state explicit. `onDismissRequest` closes that state; if a flow intentionally disallows dismissal, it must provide a reachable explicit exit/acknowledgment path.
- Preserve the target component's exit lifecycle. If it supports controlled `show` animation, keep it composed until exit/finished cleanup is complete; do not add an outer conditional that cuts that path off. Use the target-supported session pattern when the API differs.
- Reset closing-session content or pressed state after exit when cleanup must wait. A trigger's hold-down state is distinct from requested visibility: it stays active through exit and releases in the finished callback, unless the surface has reopened. A cancelled older exit must not clear a new session's pressed state or draft.
- One ordinary popup flow has one active surface. A deliberate popup-to-popup transition needs explicit dismissal/focus sequencing; do not open independent surfaces with unrelated Booleans and rely on internal z-order.
- Check the actual root-host flag for nested page Overlays. A page-local surface must not silently resolve to a global host when that changes clipping, lifetime, or focus semantics.
- In nested hosts, set the verified root-host option explicitly when the surface must stay local. The exact option name/default and which popup families expose it belong to `miuix-components`; do not infer them from a similarly named component.
- Input surfaces use the actual window's IME policy and component margins. Inspect the Android window/Manifest resize policy when relevant; do not add `adjustResize` globally or copy negative-padding compensation without target evidence.
- Focus enters an active modal surface and returns to the trigger when appropriate after dismissal. Verify back/outside/pointer/keyboard behavior and the wrapper's merged semantics on supported targets.
- For ordinary mutually exclusive popup flows, use one explicit active-surface identity and derive visibility from it. Keep queued state only when a real sequential flow needs it; internal z-order is not a workflow contract.

## Dialogs and actions

- A two-action confirmation uses Cancel then Confirm, equal button weights, and a `20.dp` gap in the standard horizontal row. Confirm uses the verified primary TextButton Defaults; keep the same semantic ordering for RTL layout.
- Standard single-input dialogs leave `16.dp` between field/content and action row. A shared two-field padding editor uses a `12.dp` inter-field gap and `24.dp` before actions. These are dialog-content metrics, not page gutters or substitutes for component inside margins.
- Bound long text/lists by the available window height and keep actions reachable while content scrolls. A fixed list cap may supplement the viewport limit; it cannot replace it in a short/IME-constrained window.
- Multi-select dialogs edit a draft set; confirmation submits that set once. Single-choice dropdowns use their supported selection contract. A picker with manual input plus remote suggestions may deliberately keep a separate Confirm action; do not force every single-choice workflow to commit immediately.
- Prevent duplicate requests/confirmation while an operation is busy. Remote suggestions must not erase a valid manually entered or current value merely because it is missing from the response.
- Feedback for an operation initiated in a separate window must remain visible in that window's lifetime/coordinate space. Use a supported host or a narrowly owned popup when the page Snackbar is covered; do not add ad-hoc offsets to every caller.
- Destructive clear/reset/delete flows state the affected scope and request confirmation where the action is not trivially reversible. Update visible data and report success only after the operation result is known.

## Sheets and reordering

- Constrain sheet height to the current window's usable height, including the relevant status/caption/cutout top boundary. Account for component-owned IME and system insets once; do not turn fixed bottom padding into a universal safe-area solution.
- Keep header cancel/confirm actions reachable. Allocate the list's maximum height from the remaining viewport after header/actions, recomputing on resize. A reusable reorder list can default to a `420.dp` cap, further reduced to the available height.
- Sheet inside margins are component/version contracts. Use supported parameters first. A full-bleed measurement/padding adapter is allowed only when the verified component requires it and belongs in one shared wrapper, not copied as a universal negative-padding trick.
- Reorder and selection are separate draft values. Use stable entity keys for both the lazy list and drag controller; move by identity/order while preserving membership. On confirm, emit selected entities in draft order.
- If at least one choice is required, enforce that invariant during edits and at final commit. Cancel discards selection/order changes; removing or adding available entities must reconcile the draft explicitly.
- Coordinate drag, list scroll, and sheet dismissal. If the inner drag list owns the gesture, configure the competing sheet nested-scroll path deliberately; do not disable every gesture globally.
- Drag start and actual reordering may use the shared haptic policy. Reordering uses the current move callback, keeps the active row pressed, and must not turn the handle into an inaccessible decorative affordance.

## Validation

Exercise parent-off settings, independent row/switch actions, first-open and reopen drafts, invalid numeric input, cancel/back/outside dismissal, long/short-window/IME layouts, search cancel and no-match states, and selection/order continuity after drag/filter/resize. Verify persistence and external-consumer behavior separately from the visible control state.
