# State, Data, and Performance Rules

Mandatory for stateful UI, preferences, async work, repeated lists/images, motion, external actions, permissions, import/export, and operation feedback. Apply the [shared scope and exception contract](design-system.md#scope-and-rule-strength). These rules cover UI-facing behavior and data ownership; they do not prescribe an unrelated application backend or platform-specific runtime architecture.

Contents: [ownership](#state-ownership-and-lifecycle) · [persistence](#persistence-and-external-consumers) · [async](#async-work-and-entry-cost) · [lists](#lists-and-image-loading) · [feedback](#loading-errors-and-feedback) · [motion](#motion-and-high-frequency-state) · [external actions](#external-actions-and-permissions) · [import/export](#import-export-and-diagnostics) · [validation](#validation).

## State ownership and lifecycle

- Persistent/business state belongs in the screen/model/repository owner; short-lived popup/pressed/expanded presentation belongs near its renderer. `rememberSaveable` is for supported restorable presentation/drafts, not a replacement for saved application configuration.
- Give each async task, listener, and state stream one explicit owner. Page actions use a page-bound scope; data that must outlive that page uses the appropriate existing longer-lived owner.
- Android screen Flow observation uses `collectAsStateWithLifecycle()` where supported; other targets use an equivalent owner/visibility policy. Raw `collectAsState` needs an intentional always-active scope. Lifecycle-aware collection does not by itself prove the upstream producer stops; configure its sharing/work lifetime separately.
- Register preference, platform, and other listeners in an effect tied to their actual owner; unregister the same instance on disposal. Include changing context/source identity in remembered adapters where needed.
- Remembered callbacks must see current state, localization, and actions. Use real keys or `rememberUpdatedState` instead of capturing the initial callback forever or restarting a controller on every lambda identity change.
- Publish coherent snapshots. Do not mutate ordinary collections behind an unchanged state object or mark mutable data `@Immutable` to hide it. Use stable/immutable models only when their contracts hold; a particular persistent collection dependency or annotation is not universally mandatory.
- Navigation, Snackbar emission, persistence writes, focus requests, and async launches happen in explicit event/effect paths, not as direct side effects of recomposition.
- A CompositionLocal carries genuinely tree-scoped context such as a backdrop/capability or coordinator, with an explicit provider, lifetime, and absence policy. Ordinary screen data/events stay explicit; do not add a mutable global/local merely to hide one level of parameter passing.
- When classic/advanced renderers coexist, resolve capability, user preference, and any temporary rollout policy at one application boundary. Both renderers consume the same semantic state and callbacks; do not duplicate refresh, navigation, selection, or draft state per renderer. Keep capability fallback distinct from an explicit product choice, and retain a compatibility branch only while a real supported condition needs it.

## Persistence and external consumers

- Keep UI display, local persistence, and any service/remote consumer on the same typed configuration contract. A local save and an external apply are distinct results.
- Use one shared save/synchronization adapter for repeated settings. It resolves scoped keys, handles supported types, and dispatches through the existing consumer bridge. Do not scatter separate local/remote writes with different defaults across rows.
- Validate numbers and normalize dependent/mutually exclusive values consistently on ordinary writes, restore/import, migration, and runtime reads. Invalid/non-finite numeric values follow the declared default/range policy.
- Preserve stable keys, default values, and storage types when moving UI. A renamed title or reordered dropdown must not reinterpret persisted values. Prefer stable values/IDs when defining a new choice contract.
- Observe external changes when they are allowed during the page lifetime. Report unavailable consumers, failed operations, and required restarts explicitly; do not present an optimistic toggle as proof of runtime application.
- If immediate apply is required, cover the full path from input through save, synchronization, consumer refresh, and resulting state. Source inspection is not evidence that another process/device actually received it.

## Async work and entry cost

- Keep navigation chrome and a loading/content skeleton immediately composable. Package scans, icon decoding, disk/database access, network discovery, and large transforms must not run synchronously in composition or on its hot UI path.
- Move blocking work to the appropriate worker context. Stage expensive content composition around actual entry readiness when needed; do not require a fixed sleep or fake loading delay on every page.
- A loading screen can defer presentation while a background owner prepares data. Do not confuse waiting for a transition with withholding all IO, or mistake a nominal delay for proof that a transition finished.
- Reuse loaded snapshots and serialize/deduplicate equivalent loads where necessary. Refresh is explicit; precomposed pager tabs must not launch redundant scans on every visit.
- Capture request identity/parameters and reject obsolete results when query, item, configuration, or owner changes. Cancel superseded jobs or serialize them through the existing owner; an older job's `finally` must not clear a newer job's busy flag.
- Preserve coroutine cancellation. Rethrow `CancellationException` when handling expected failures and reset the current request's loading state in its completion/finalization path.
- A request failure retains useful prior content or the editing draft when appropriate. Do not replace a failed scan with an empty success result or report success just because dispatch began.

## Lists and image loading

- Dynamic lazy items use stable entity keys such as package, record, or persisted item IDs. Do not use a shifting index, translated label, or non-unique text when insertion/filtering/reordering changes identity. Distinguish reused row structures with `contentType` when relevant.
- Keep large repeated collections lazy. Small fixed settings groups can share one Card; do not require a custom segmented-card helper for every group.
- Filtering, sorting, chunking, and model construction depend on actual source/query/filter/sort/locale inputs. Cache expensive transforms outside lazy-content builders where repeated unrelated state would otherwise redo them.
- Do not retain item state under an identity that changes its meaning. Hoist selection/expanded/order state when rows can leave composition and later return; keep each tab's list state independent.
- Decode icons off the composition path at the intended display size. Cache by entity plus relevant update/version identity and requested pixel size; use a bounded cache rather than keeping every full-size image forever.
- Keep placeholder and loaded image geometry identical. A missing package/icon is a row-level state, not an exception that aborts the entire list. Cancel/ignore image results for obsolete row identities.
- Build a complete result off the hot path and publish it coherently where practical. Avoid repeated package queries and one global state update per decoded icon when a cached row-level image state would suffice.

## Loading, errors, and feedback

- Model initial loading, refreshing existing data, empty success, populated success, unavailable capability, and failure/retry explicitly. Use the states the feature actually needs rather than conflating them into one Boolean or empty collection.
- Initial loading shows the shared progress treatment, normally `InfiniteProgressIndicator` for an indefinite load, inside the correctly padded content region. Keep the app bar and navigation usable.
- Refresh keeps useful current content where the operation supports it and uses hoisted refresh state with localized labels. Completion and failure both end loading; a disabled duplicate action cannot start a second request.
- Persistent/blocking failures render inline with explanation and retry. Empty content explains absence; unavailable dependencies/permissions explain the prerequisite instead of claiming there are simply no results.
- Use Miuix Snackbar for ordinary transient operation feedback; DO NOT introduce Toast for these in-app flows. Supply a host that is visible in the active page/window and not covered by navigation bars, IME, or a separate dialog.
- Resolve localized message/action text outside unkeyed remembered callbacks. Prevent repeated-tap feedback floods when the same operation is already busy or a matching message is pending.
- Destructive mutation, copy/export/import, and remote discovery report their actual outcome. Preserve input/current data on failure and expose a useful recovery action when available.

## Motion and high-frequency state

- Reuse component motion/feedback before adding custom transitions. Animate the property that owns the change: content size for bounded expansion, item movement for stable lazy identity, layer transforms for visual-only movement.
- Use `derivedStateOf` for genuinely coarser signals such as a scroll threshold or fully-collapsed Boolean. A per-frame numeric value wrapped in `derivedStateOf` is not inherently throttled.
- When an animation affects only placement or drawing, pass a state/lambda to the layout/draw/layer consumer where practical rather than invalidating the whole screen each frame. Do not force custom layout code when a supported component already owns the behavior.
- Tie infinite effects to the active/visible owner when they have a recurring cost. An offscreen pager page or alpha-zero surface must not be assumed to suspend its coroutine automatically.
- Keep cover rotation/flip/blur, image clipping, and shadow geometry coherent. Capability fallback removes unsupported effects while preserving the same content and interactions.
- Preserve continuity on rapid toggle/reopen/cancel. Do not use changed lazy keys or discarded draft state as a substitute for an animation transition.
- Do not claim lower CPU/GPU cost, fewer layers, or smoother frames solely from a modifier name or source refactor. Performance claims require relevant runtime measurements.

## External actions and permissions

- Open external links/system pages through a shared application adapter that handles unavailable handlers and denied launches. Keep a visible failure path; opening an Intent is not proof that the requested operation succeeded.
- A failed link launch can offer a localized copy-link Snackbar action. Clipboard work and its result feedback follow the same page/window owner.
- Check actual permission/service/capability state before enabling a dependent operation. Show the prerequisite/recovery flow; confirmation checks the state again rather than treating the sheet button or a previous launch as a grant.
- Keep requested permission, listener/service authorization, feature preference, and observed running state distinct. Returning from settings or receiving a result updates the appropriate owner; do not unconditionally flip all flags.
- Keep platform-specific launchers and capability checks in the platform/application layer. Shared visual controls receive semantic state/actions, not duplicated Intent/package-manager code.

## Import, export, and diagnostics

- Use the platform's document/result contract where applicable. A null/cancelled picker result is cancellation, not an import failure or successful export.
- Read/write documents off the UI hot path and close streams. Report success only after the actual write/parse/apply outcome; offer retry or a useful error when it fails.
- Version serialized configuration, validate structure/types, and apply the same defaults/ranges/invariants as ordinary edits. Do not restore arbitrary JSON numbers under a different preference storage type.
- Shared backup/export policy decides what is included. Credentials and explicitly excluded/local-only values are not silently added to exported configuration by a generic preference dump.
- Export the intended active/filtered dataset as a stable operation snapshot. A tab/filter change during the document picker must follow a defined policy rather than accidentally selecting a different dataset.
- Destructive clear/reset actions use the same busy/result/confirmation rules as other operations. Update affected list/detail state coherently after completion or return navigation.

## Validation

Check relevant first-load/refresh/error/retry states, cancellation and obsolete-result protection, lifecycle exit/reentry, icon placeholders and cache invalidation, locale changes, persisted configuration, and external-action cancellation/failure. Keep source, compilation, UI rendering, real consumer behavior, and performance measurements as separate evidence categories.
