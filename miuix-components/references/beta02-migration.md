# Miuix `0.9.5-beta01` → `0.9.5-beta02`

This note records the upgrade delta that affects Skill decisions. The maintained target is now Miuix `0.9.5-beta02`, tag `v0.9.5-beta02`, commit `66248bacc7be29ae6bdc0957de9458b42ac67732`. It is a prerelease coordinate; do not mix beta01 and beta02 contracts in one answer.

## Build and dependency baseline

| Area | beta01 | beta02 | Skill consequence |
|---|---|---|---|
| Compose Multiplatform | `1.12.2` | `1.13.0-rc01` | Recheck compiler and Compose compatibility before copying a consumer build setup. |
| Gradle wrapper | `9.9.1` | `9.9.2` | Use the beta02 wrapper when validating the upstream checkout. |
| Kotlin / AGP / SDK | Kotlin `2.4.21`, AGP `9.5.1`, compile SDK 37 minor 2, Build Tools `37.0.0`, JDK 21 | unchanged | The Android SDK and JDK contract remains the same. |
| Example-only dependencies | no `hiddenapibypass` catalog entry | `org.lsposed.hiddenapibypass:hiddenapibypass:6.2` is catalogued for the Example | Do not add this dependency to an application unless its own Android runtime requirement calls for it. |
| Repository resolution | Gradle plugin portal appeared before Maven Central in build logic | Maven Central is preferred | Keep `mavenCentral()` available for consumer dependency resolution. |

## Public API and behavior changes

- `TextField` keeps the `TextFieldState` overload as the preferred API. The `String` and `TextFieldValue` overloads are now deprecated. Use `InputTransformation`, `OutputTransformation`, `TextFieldLineLimits` and `onKeyboardAction` when migrating state-based input. Validation still has no built-in `isError` or `supportingText` parameter.
- `LinearProgressIndicator` and `CircularProgressIndicator` add lambda-based `progress: () -> Float` overloads that read progress during drawing. Prefer them for frame-rate progress updates to avoid recomposing the indicator subtree.
- `ScrollBar` was redesigned around `ScrollableState.scrollIndicatorState`. `rememberScrollBarAdapter` accepts a `ScrollableState` and supports scroll, lazy list/grid/staggered grid and pager states. `Modifier.verticalScrollBar` and `Modifier.horizontalScrollBar` are available, `reverseLayout` on `VerticalScrollBar`/`HorizontalScrollBar` is compatibility-only, and the default touch target is now `24.dp` rather than `48.dp`.
- `NavigationBar` and `FloatingNavigationBar` now account for horizontal display-cutout/navigation-bar insets. Floating bars combine navigation-bar and animated caption-bar values for their own bottom spacing; `defaultWindowInsetsPadding = false` transfers that responsibility to the caller.
- `TopAppBarLayout`, `TopAppBar` and `SmallTopAppBar` add `centerObstructionStart`, with `TopAppBarDefaults.TitleControlSpacing = 12.dp`, for titles that must avoid a center-side overlay. Beta02 also restricts idle settle to mouse-wheel input and improves title alignment/overflow handling.
- `GlassTopAppBar` follows `WindowInsets.systemBars` for its progressive band, exposes the same `centerObstructionStart`, gates progressive capture through the scroll mask, and uses dynamic material/fallback selection for action buttons.
- `GlassSearchOverlay` and `GlassTextField` are new public Glass components. The overlay owns search focus, Back/Escape dismissal, query state and a progressive blur header. `GlassTextField` mirrors `TextField` and supplies translucent glass colors, with state-based overloads preferred.
- `GlassSegmentedTabRow` retains horizontal drag selection with release-time settling and keyboard/accessibility activation, but beta02 drives its material ramp from the resolved backdrop with an animated transition and uses the dynamic theme's primary selected-content color. Keep its required non-null backdrop and synchronize its selection with the pager owner.
- `GlassFloatingActionButtonDefaults.Size` changes from `60.dp` to `56.dp`, and the button now scales down on press. `GlassNavigationRail` keeps the existing dynamic-theme selected-color default; do not infer a color change from the other Glass material updates.
- `miuix-nav` keeps the route/back-stack contract. Focus clearing on top-entry changes remains part of `NavDisplay`; beta02 changes the internal effect implementation, not the route ownership rule.

## Upgrade rules for this Skill

1. Confirm the resolved artifact is `0.9.5-beta02` before applying any local contract.
2. Read the beta02 official doc for the selected component, then use the matching contract note for host, inset, state and fallback behavior.
3. Do not copy a beta01 value overload, 48.dp scrollbar hit target, old floating-bar inset assumption, or old Glass FAB size into a beta02 answer.
4. Keep the existing greenfield shell rule: peer destinations still share one selection coordinator and pager, while `NavDisplay` owns detail routes and back-stack transitions.

## Evidence

- [Miuix beta02 tag](https://github.com/compose-miuix-ui/miuix/tree/66248bacc7be29ae6bdc0957de9458b42ac67732)
- [beta02 setup and build files](https://github.com/compose-miuix-ui/miuix/blob/66248bacc7be29ae6bdc0957de9458b42ac67732/gradle/libs.versions.toml)
- [beta02 TextField source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TextField.kt)
- [beta02 ScrollBar source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/ScrollBar.kt)
- [beta02 navigation-bar source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/NavigationBar.kt)
- [beta02 top-bar source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/TopAppBar.kt)
- [beta02 GlassSearchOverlay documentation](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/glass/glasssearchoverlay.md)
- [beta02 GlassTextField documentation](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/docs/components/glass/glasstextfield.md)
- [beta02 Glass navigation-bar source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassNavigationBar.kt)
- [beta02 Glass segmented tabs source](https://raw.githubusercontent.com/compose-miuix-ui/miuix/66248bacc7be29ae6bdc0957de9458b42ac67732/miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/GlassTabRow.kt)
