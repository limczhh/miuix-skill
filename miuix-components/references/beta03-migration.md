# Miuix `0.9.5-beta02` → `0.9.5-beta03`

This note records only the upgrade delta that changes Skill decisions. The maintained target is now Miuix `0.9.5-beta03`, tag `v0.9.5-beta03`, commit `3526cfe2ca0779c5a2eab99af8737a96c9a8a885`. Do not mix beta02 and beta03 contracts in one answer.

## What changed

| Area | beta02 → beta03 | Skill consequence |
| --- | --- | --- |
| Build/tooling | The upstream wrapper remains Gradle `9.9.2`; AGP moves to `9.5.2`, Compose Hot Reload to `1.3.0`, and Spotless to `8.12.0`. The Example adds `androidx.metrics:metrics-performance:1.0.0` for its Android `JankStats` monitor. | Use beta03 coordinates and source when checking the library. Do not copy Example-only metrics dependencies into a consumer unless the consumer actually uses `JankStats`. |
| `GlassTopAppBar` public API | Both public overloads remove `visualContentOffset`. The scroll-behavior overload derives overlap from `contentOffset` and negative `overscrollOffset`; the explicit overload combines `isContentScrolled` with negative overscroll. The band uses `collapsedFraction` when an expandable bar has a `ScrollBehavior`, otherwise the short state transition mask. | Remove `visualContentOffset` call arguments and do not recreate a private visual-offset bridge for the bar. Keep one `ScrollBehavior`, attach its nested-scroll connection to the content, and bridge `overscrollOffset` only through the supported overscroll path. `GlassTopAppBarDefaults.collapseRamp` remains a separate helper for custom consumers; it is no longer a `GlassTopAppBar` parameter or the built-in bar's source of progress. |
| `GlassTabRow` / `GlassSegmentedTabRow` | The rows keep their public selection contracts, but beta03 passes the resolved `surfaceAlpha` ramp to the Glass modifier's `enabled` flag. It also fixes list resting opacity and fade-out flicker. | On a shader-capable runtime, `surfaceAlpha = 0f` is now the supported material-off branch for both rows: retain selection, semantics, resting fills/track and indicator while disabling the sampled Glass pipeline. For `GlassTabRow`, resolve a null effective backdrop when no inherited top-bar backdrop should be used; the segmented row still requires a non-null argument. |
| Glass/blur pipeline | `miuix-blur` and `miuix-glass` reduce recomposition and allocation pressure through value-stable pipelines, lazy shader-key creation, cached blur parameters and node-based shadows/press handling. | Keep remembered, stable inputs and avoid rebuilding custom effect descriptions every recomposition. Treat this as source-level optimization, not proof of an application-wide CPU/GPU or energy gain; measure the target runtime separately. |
| Glass navigation and ordinary tabs | Navigation bar/rail layout and inset handling, Glass tab width/indicator settling, and sidebar rendering were reorganized for resize and recomposition behavior. Public ownership and selection APIs remain the same. | Continue to hoist route/selection/state and keep one inset owner. Do not copy beta03's internal width or bottom-padding calculations into an application wrapper; verify exact target behavior when a shell depends on pixel-level placement. |

## Example-only changes

The release also changes the upstream Example. Treat these as integration patterns, not new library APIs:

- `GlassPage` derives top-bar material from the Boolean overlap boundary (`canScrollBackward` or negative overscroll), uses `surfaceAlpha = 0f` when a tab row is outside the top bar, and resets list/top-bar offsets when the tab-row placement changes.
- `IconPage` removes its private signed visual-offset calculation and uses the beta03 `GlassTopAppBar` state contract.
- The Example splits `LazyColumn` cards into per-row items and gates the compact-mode backdrop; these are Example composition/performance choices, not new consumer APIs.
- The Android Example adds a `JankStats` frame monitor and `ThemeSettingsPage`; the metrics dependency is Example-only unless the consumer explicitly adopts that monitor.

## Migration checks

1. Confirm the resolved artifacts are `0.9.5-beta03` and inspect local substitutions or `includeBuild` before applying this contract.
2. Read the beta03 official Markdown for the selected component, then use the matching contract note for host, inset, state and fallback behavior.
3. Search the consumer for `GlassTopAppBar(... visualContentOffset = ...)`; replace it with the explicit boolean/state and the target's real `ScrollBehavior`/overscroll owner.
4. For an effects setting, use `surfaceAlpha = 0f` on either Glass tab row when the intended result is the row's resting visual state. Do not use `alpha = 0f` or a zero blur radius as a universal effect switch.
5. Keep API evidence, compilation, rendering/device behavior and performance measurements separate. The beta03 release notes and source changes do not prove a consumer's visual or performance result.

## Evidence

- [Miuix beta03 release](https://github.com/compose-miuix-ui/miuix/releases/tag/v0.9.5-beta03)
- [Miuix beta03 tag](https://github.com/compose-miuix-ui/miuix/tree/3526cfe2ca0779c5a2eab99af8737a96c9a8a885)
- [beta03 setup and build files](https://github.com/compose-miuix-ui/miuix/blob/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/gradle/libs.versions.toml)
- [beta03 GlassTopAppBar source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassTopAppBar.kt)
- [beta03 Glass tab source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassTabRow.kt)
- [beta03 Glass navigation bar source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassNavigationBar.kt)
- [beta03 blur backdrop source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/miuix-blur/src/commonMain/kotlin/top/yukonga/miuix/kmp/blur/DrawBackdropModifier.kt)
