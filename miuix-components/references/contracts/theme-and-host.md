# Theme and Page Host Contracts

This is a focused, source-verified API contract guide for the maintained Miuix snapshot: upstream main at commit d8efe1872c08c863a8b33e7044dd2a8b43038032 (v0.9.4-80-gd8efe187, not a release tag).

Use when the task needs the application theme root or the page Scaffold content/inset contract.

Do not use when the task only needs a component family, or it changes page composition without needing an exact host declaration.

Before copying a call:

- Check the target project's resolved Miuix version, source set, and dependency substitution.
- Treat the declaration, package/import, module, defaults, state/callback direction, and host requirement as one contract.
- An omitted parameter is not permission to invent an overload or transfer a Material parameter.
- source-verified means the pinned declaration was checked; compile-verified requires compiling the affected target.

The exact source path is part of each contract below. If the target version differs, re-run the source-verification gate before presenting version-sensitive code.
## Theme

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/MiuixTheme.kt`, `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/theme/ThemeController.kt`  
**Documentation:** `docs/guide/theme.md`

The controller overload is the normal application-root call. `ThemeController` is remembered by the root owner; it is not recreated by each page.

```kotlin
@Composable
fun MiuixTheme(
    controller: ThemeController,
    textStyles: TextStyles = MiuixTheme.textStyles,
    content: @Composable () -> Unit,
)

@Composable
fun MiuixTheme(
    colors: Colors = MiuixTheme.colorScheme,
    textStyles: TextStyles = MiuixTheme.textStyles,
    content: @Composable () -> Unit,
)

@Stable
class ThemeController(
    colorSchemeMode: ColorSchemeMode = ColorSchemeMode.System,
    lightColors: Colors = lightColorScheme(),
    darkColors: Colors = darkColorScheme(),
    keyColor: Color? = null,
    colorSpec: ThemeColorSpec = ThemeColorSpec.Spec2021,
    paletteStyle: ThemePaletteStyle = ThemePaletteStyle.TonalSpot,
    isDark: Boolean? = null,
)
```

Minimal root call:

```kotlin
import androidx.compose.runtime.Composable
import androidx.compose.runtime.remember
import top.yukonga.miuix.kmp.theme.ColorSchemeMode
import top.yukonga.miuix.kmp.theme.MiuixTheme
import top.yukonga.miuix.kmp.theme.ThemeController

@Composable
fun App() {
    val controller = remember { ThemeController(ColorSchemeMode.System) }

    MiuixTheme(controller = controller) {
        // Application shell and pages.
    }
}
```

Do not add a second page-level `MiuixTheme` only to obtain colors or text styles.

## Page host

**Module:** `miuix-ui`  
**Source:** `miuix-ui/src/commonMain/kotlin/top/yukonga/miuix/kmp/basic/Scaffold.kt`  
**Documentation:** `docs/components/basic/scaffold.md`
**Demo:** `docs/demo/src/commonMain/kotlin/ScaffoldDemo.kt`

Miuix import:

```kotlin
import top.yukonga.miuix.kmp.basic.Scaffold
```

Exact public declaration:

```kotlin
@Composable
fun Scaffold(
    modifier: Modifier = Modifier,
    topBar: @Composable () -> Unit = {},
    bottomBar: @Composable () -> Unit = {},
    floatingActionButton: @Composable () -> Unit = {},
    floatingActionButtonPosition: FabPosition = FabPosition.End,
    floatingToolbar: @Composable () -> Unit = {},
    floatingToolbarPosition: ToolbarPosition = ToolbarPosition.BottomCenter,
    snackbarHost: @Composable () -> Unit = {},
    popupHost: @Composable () -> Unit = { MiuixPopupHost() },
    containerColor: Color = MiuixTheme.colorScheme.surface,
    contentWindowInsets: WindowInsets = WindowInsets.systemBars.union(WindowInsets.displayCutout),
    content: @Composable (PaddingValues) -> Unit,
)
```

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
