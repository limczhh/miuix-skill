# Theme and Page Host Integration Notes

Read the selected official documentation for imports, full parameters/defaults and basic usage. These notes retain rc01 integration pitfalls and selected composition examples; they are not a replacement API manual. Follow the [documentation-first workflow](../source-verification.md#documentation-first-path).

## Theme

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/MiuixTheme.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/ThemeController.kt`  
**Documentation:** `docs/guide/theme.md`

The controller overload is the normal application-root call. `ThemeController` is remembered by the root owner; it is not recreated by each page.

Read the pinned documentation/source: [MiuixTheme / ThemeController](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/guide/theme.md).

Do not add a second page-level `MiuixTheme` only to obtain colors or text styles.

The rc01 snapshot's seed-generated Monet colors use spec 2026 for `ThemePaletteStyle.Cmf`; `ThemeColorSpec` has no `Spec2026` member. Palette style/spec are used by `colorsFromSeed` only when a Monet mode has a non-null `keyColor`. With a null key, Monet delegates to `platformDynamicColors(dark)` without those arguments; System/Light/Dark use the supplied light/dark schemes instead. Do not promise a palette change in those other paths. The theme installs `ProvideDisplayCutoutFix`, controlled through `LocalDisplayCutoutFixEnabled`, and configures `MiuixIndication` with theme-aware press/hover/focus alphas (`MiuixIndication` itself also draws before content and holds brief taps for ~200 ms). These are version-specific behaviors, not extra required arguments on `MiuixTheme` or `ThemeController`.

## Page host

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Scaffold.kt`  
**Documentation:** `docs/components/basic/scaffold.md`
**Demo:** `docs/demo/src/commonMain/kotlin/ScaffoldDemo.kt`

Read the pinned documentation/source: [Scaffold](https://raw.githubusercontent.com/compose-miuix-ui/miuix/542463a0863b393a3d6052237daae6c3da217e70/docs/components/basic/scaffold.md).

Minimal content call:

```kotlin
Scaffold(
    topBar = { TopAppBar(title = "Page") },
) { innerPadding ->
    Column(
        modifier = Modifier
            .padding(innerPadding)
            .consumeWindowInsets(innerPadding),
    ) {
        // Primary page content.
    }
}
```

The `content` lambda receives `PaddingValues`; apply and consume them at one known content boundary. For a `LazyColumn`, use the same boundary for its `contentPadding`/inset consumption. If using `Modifier.verticalScroll`, apply the padding to the scroll child rather than to the scroll modifier itself. A top or bottom bar may already own its own default window-inset padding; do not add the same system inset again to every child.
