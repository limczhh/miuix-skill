# State, Data, and Performance Rules

Mandatory for stateful UI, persistence, async work, lists/icons, feedback, motion, external actions, permissions, and import/export (see [Scope & Exceptions](design-system.md#scope-and-rule-strength)).

Contents: [ownership](#state-ownership-and-lifecycle) · [persistence](#persistence-and-external-consumers) · [async](#async-work-and-entry-cost) · [lists & icons](#lists-and-image-loading) · [feedback & errors](#loading-errors-and-feedback) · [motion](#motion-and-high-frequency-state) · [external & permissions](#external-actions-and-permissions) · [import/export](#import-export-and-diagnostics) · [validation](#validation)

## State Ownership and Lifecycle

- Keep business/persisted state in screen/model/repository owners; transient popup/pressed/expanded UI state near renderers (`rememberSaveable` for restorable drafts/presentation only, never config storage).
- Give async jobs, listeners, and collectors explicit scopes (`collectAsStateWithLifecycle()` on Android screens; unregister listeners with the same owner). Remembered callbacks must see current state/strings via proper keys or `rememberUpdatedState`.
- Publish coherent snapshots (never mutate collections inside `@Immutable` holders or mark mutable classes `@Immutable`). Never emit navigation, Snackbar, persistence writes, focus requests, or async jobs directly inside recomposition.
- Restrict `CompositionLocal` to genuine tree-scoped context (backdrop/theme/coordinator) with explicit absence policy; keep ordinary data explicit.
- When classic/Glass renderers coexist, resolve capability/preference once above render branches and share **1** route/pager/navigation/draft owner (never clone state per renderer; distinguish runtime fallback from explicit preference).

## Persistence and External Consumers

- Share 1 typed config contract & adapter across UI, storage, migration/import, and runtime consumers (never scatter mismatched writes/defaults). Normalize invalid/non-finite numeric values consistently; keep persisted keys/IDs independent of UI titles/index order.
- Distinguish local save from external/service application (observe live consumer updates when needed; explicitly report missing consumer, failure, or required restart).

## Async Work and Entry Cost

- Keep navigation chrome and loading skeletons immediately composable — **never** decode icons, scan packages, hit disk/DB, or block synchronously in composition/hot UI path. Avoid fake fixed `delay()` screens; stage UI readiness cleanly.
- Deduplicate/reuse snapshots across precomposed pager tabs; match request tokens/IDs so obsolete async results or older `finally` blocks never clear newer busy flags. Rethrow `CancellationException`; retain valid prior content or drafts on failure instead of overwriting with empty success.

## Lists and Image Loading

- Dynamic lazy lists **MUST** use stable unique entity keys (never shifting index or translated text) and `contentType` where row layouts vary. Keep large/repeated lists lazy; cache heavy filter/sort/transform steps outside `LazyListScope` keyed on real inputs (query, filter, sort, locale).
- Hoist selection/expanded/reorder state out of items that leave composition; keep each tab's scroll state separate.
- Decode app/icon bitmaps off the composition thread at target display px size using a bounded cache keyed by entity + update/version + size; keep fixed-size placeholders so async loading never shifts layout; treat single-item icon errors gracefully without aborting the list.

## Loading, Errors, and Feedback

- Model initial load (typically `InfiniteProgressIndicator` inside padded content while bars stay interactive), pull-refresh (retaining content, localized labels, disabling duplicate launches), empty state, unavailable capability/permission (explain prerequisite), and failure/retry cleanly — do not collapse everything into one Boolean.
- Use **Miuix Snackbar** (visible host unobstructed by nav bars, IME, or popups) for transient in-app operation feedback — **DO NOT** use `Toast`. Resolve Snackbar text/actions outside unkeyed remembered blocks and prevent tap-flooding duplicate messages. Preserve drafts/state on destructive/export/discovery failures with recovery actions where possible.

## Motion and High-Frequency State

- Prefer stock component motion; animate the owning layer (`transform`/alpha in draw/graphicsLayer or deferred lambdas) rather than invalidating composition every frame. Use `derivedStateOf` only for coarser threshold signals (`scroll > 0`), not continuous floats.
- Bind infinite coroutines/animations to active visibility (offscreen pager pages or `alpha = 0f` do not auto-cancel effects). Keep clip/shadow/fallback geometry coherent across rapid toggles/cancels without swapping lazy keys. Performance claims require real runtime measurements, not refactor intent alone.

## External Actions and Permissions

- Route external links/settings through a shared launcher adapter handling missing handlers/denials (with Snackbar fallback such as copy-link). Keep permission request, system authorization, feature preference, and observed service state distinct; re-verify actual permission/capability before dependent execution rather than assuming launching Settings granted it. Keep Intent/package-manager code out of pure UI controls.

## Import, Export, and Diagnostics

- Cancelled document pickers are cancellations, not errors. Perform file IO/parsing off UI thread and close streams; validate serialized config version/types/invariants on import using ordinary bounds; exclude credentials/local-only keys from generic backups; snapshot intended export data explicitly; follow standard confirmation/feedback rules for destructive resets.

## Validation

Test initial load/refresh/error/retry, job cancellation/token guards, tab reentry/state retention, icon cache invalidation, locale switches, persisted config round-trip, and external-intent failure/cancellation separately from source checks.
