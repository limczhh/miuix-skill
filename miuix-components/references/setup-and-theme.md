# Setup, Theme, Host, and Color Lookup

Use for dependencies, platform/SDK constraints, `MiuixTheme` / `ThemeController`, `Scaffold`, `@Preview`, and visual-to-token color resolution at pinned `0.9.5-rc02`.

Contents: [dependencies & SDK](#dependencies-and-platform-constraints) · [theme & indication](#theme-controller-and-indication) · [typography](#typography-miuixthemetextstyles) · [Scaffold host](#scaffold-host) · [Preview](#android-studio-preview) · [color rules](#color-semantics-and-visual-lookup) · [visual lookup](#visual-to-semantic-lookup) · [component defaults](#component-color-defaults) · [core roles](#core-color-roles)

## Dependencies and Platform Constraints

Upstream `0.9.5-rc02` build versions: Kotlin `2.4.21`, Compose Multiplatform `1.13.0-rc01`, kotlinx-serialization `1.13.0`, navigation-event `1.2.0`, AGP `9.5.3`, Gradle `9.9.2`, JDK 21 (Example additionally uses `androidx.metrics:metrics-performance:1.0.0` for Android `JankStats`, only add if needed).

**Compose Multiplatform (`commonMain` / `desktopMain`):**

```kotlin
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-ui:0.9.5-rc02")
            implementation("top.yukonga.miuix.kmp:miuix-preference:0.9.5-rc02") // optional
            implementation("top.yukonga.miuix.kmp:miuix-icons:0.9.5-rc02") // optional
            implementation("top.yukonga.miuix.kmp:miuix-glass-icons:0.9.5-rc02") // optional Glass icons
            implementation("top.yukonga.miuix.kmp:miuix-material:0.9.5-rc02") // optional direct material types
            implementation("top.yukonga.miuix.kmp:miuix-blur:0.9.5-rc02") // optional; Android requires API 33
            implementation("top.yukonga.miuix.kmp:miuix-squircle:0.9.5-rc02") // optional standalone use
            implementation("top.yukonga.miuix.kmp:miuix-glass:0.9.5-rc02") // optional Glass family
            implementation("top.yukonga.miuix.kmp:miuix-nav:0.9.5-rc02") // optional
        }
        desktopMain.dependencies {
            implementation("top.yukonga.miuix.kmp:miuix-window:0.9.5-rc02") // optional; Desktop JVM only
        }
    }
}
```

**Pure Android (`-android` artifacts):**

```kotlin
android {
    compileSdk {
        version = release(37) {
            minorApiLevel = 2
        }
    }
    defaultConfig { minSdk = 24 }
}

dependencies {
    implementation("top.yukonga.miuix.kmp:miuix-ui-android:0.9.5-rc02")
    implementation("top.yukonga.miuix.kmp:miuix-preference-android:0.9.5-rc02") // optional
    implementation("top.yukonga.miuix.kmp:miuix-icons-android:0.9.5-rc02") // optional
    implementation("top.yukonga.miuix.kmp:miuix-glass-icons-android:0.9.5-rc02") // optional
    implementation("top.yukonga.miuix.kmp:miuix-material-android:0.9.5-rc02") // optional
    implementation("top.yukonga.miuix.kmp:miuix-glass-android:0.9.5-rc02") // optional
    implementation("top.yukonga.miuix.kmp:miuix-nav-android:0.9.5-rc02") // optional
    // miuix-blur-android declares minSdk 33 (also transitive in miuix-glass-android).
}
```

- **AAR `compileSdk` boundary**: Checked Android AAR metadata (`miuix-ui-android`, `miuix-blur-android`, `miuix-glass-android`) declares `minCompileSdk=37` and `minCompileMinorSdk=2` (not decimal `37.2`, nor an invented `compileSdkMinor`).
- **Android API 24 vs 33 (`miuix-blur` / `miuix-glass`)**: Core modules target `minSdk = 24`; `miuix-blur-android` declares `minSdk = 33`. Apps with `minSdk < 33` using `miuix-glass` must explicitly add `<uses-sdk tools:overrideLibrary="top.yukonga.miuix.kmp.blur" />` in `AndroidManifest.xml` (on `<uses-sdk>`, not composables) and preserve [component fallbacks](glass-and-blur.md#blur-controls-and-fallback-matrix).

## Theme, Controller, and Indication

Guide: `docs/guide/theme.md`. Source (`top.yukonga.miuix.kmp.theme`): `MiuixTheme.kt`, `ThemeController.kt`, `../utils/MiuixIndication.kt`.

- Use one root `MiuixTheme(controller = remember { ThemeController(ColorSchemeMode.System) })`; do not nest extra `MiuixTheme` per page merely to read tokens (`MiuixTheme.colorScheme` / `MiuixTheme.textStyles`).
- `ColorSchemeMode` options: `System`, `Light`, `Dark`, `MonetSystem`, `MonetLight`, `MonetDark`. Use fixed `lightColorScheme()`/`darkColorScheme()` only for fixed brand palettes or previews/tests.
- **Monet & `ThemePaletteStyle.Cmf`**: With a non-null `keyColor` in a Monet mode, `colorsFromSeed` applies `ThemePaletteStyle.Cmf` using internal spec 2026 (`ThemeColorSpec` publicly exposes only `Spec2021` and `Spec2025` — do not invent `Spec2026`). With `keyColor == null`, Monet delegates to `platformDynamicColors(dark)` without forwarding palette/spec.
- **Cutout fix & Indication**: Both `MiuixTheme` overloads install `ProvideDisplayCutoutFix` (gated via `LocalDisplayCutoutFixEnabled`) and supply theme-aware `pressAlpha`/`hoverAlpha`/`focusAlpha` on `MiuixIndication` (which draws its overlay *before* `drawContent()` and holds brief taps for ~200 ms). `MiuixTheme` also provides `MiuixOverscrollFactory` via `LocalOverscrollFactory`.

## Typography (`MiuixTheme.textStyles`)

Guide: `docs/guide/textstyles.md`, source: `miuix-ui/.../theme/TextStyles.kt` (`top.yukonga.miuix.kmp.theme`).

- Read via `MiuixTheme.textStyles.<role>` (NOT `MaterialTheme.typography`); all default styles use `MiuixTheme.colorScheme.onBackground` unless overridden on `Text(..., color = ...)`.
- Available 14 roles: `title1` (`32sp`), `title2` (`24sp`), `title3` (`20sp`), `title4` (`18sp`), `headline1` (`17sp`), `headline2` (`16sp`), `main` (`17sp`), `paragraph` (`17sp`, `1.2em` line height), `body1` (`16sp`), `body2` (`14sp`), `subtitle` (`14sp`, `Bold`), `button` (`17sp`), `footnote1` (`13sp`), `footnote2` (`11sp`).
- Customize globally via `defaultTextStyles(...)` passed into `MiuixTheme(textStyles = ...)`.

## Scaffold Host

Doc: `docs/components/basic/scaffold.md`, demo: `docs/demo/ScaffoldDemo.kt`. Package: `top.yukonga.miuix.kmp.basic` (`Scaffold`, `FabPosition`, `ToolbarPosition`).

```kotlin
Scaffold(
    topBar = { TopAppBar(title = "Page") },
    bottomBar = { /* NavigationBar / FloatingNavigationBar */ },
    floatingActionButton = { /* FloatingActionButton */ },
    floatingToolbar = { /* FloatingToolbar */ },
    floatingToolbarPosition = ToolbarPosition.BottomCenter,
) { innerPadding ->
    Column(modifier = Modifier.padding(innerPadding).consumeWindowInsets(innerPadding)) {
        // Apply and consume innerPadding at one primary boundary;
        // for Modifier.verticalScroll, apply padding to the scroll child, not before .verticalScroll().
    }
}
```

Use `Scaffold.floatingToolbar` + `floatingToolbarPosition` instead of overlaying a toolbar manually so Snackbar spacing remains coordinated.

## Android Studio Preview

No first-party `@Preview` exists in upstream. When previewing:
- Supply deterministic fake state, wrap in `MiuixTheme` with explicit Light or Dark `ThemeController` (avoid System/Monet), and wrap `Overlay*` components in a `Scaffold` popup host.
- Previews do not validate shader/blur, runtime back, focus/IME, or platform windows.

## Color Semantics and Visual Lookup

Guide: `docs/guide/colors.md`, source: `miuix-ui/.../theme/Colors.kt`.

1. Identify both hue and job (container, paired `on*` content, selected, error, border, divider, disabled).
2. Prefer the component's `*Defaults` factory first (e.g., dropdown selected items use `tertiaryContainer`, *not* `primaryContainer`).
3. For custom drawing/wrappers, read `MiuixTheme.colorScheme.<token>` and pair containers with matching `on*` roles (`onPrimary` = content on `primary`).
4. Do not treat light-theme hex values as permanent meanings under Monet/dynamic colors; for an ambiguous "找一个蓝色", confirm intent or assume `primary`.

## Visual-to-Semantic Lookup

Sample hex values are from non-Monet `lightColorScheme()` / `darkColorScheme()`.

| Visual description | Candidate token / API | Default light / dark | Notes |
|---|---|---|---|
| 蓝色主色 / 强调色 | `primary` | `#FF3482FF` / `#FF277AF7` | Accent role; use component primary Defaults |
| 蓝色背景上文字/图标 | `onPrimary` | white / white | Paired foreground for `primary` |
| 强调色容器与前景 | `primaryContainer` / `onPrimaryContainer` | `#FF5D9BFF` / `#FF338FE4` | Not a generic selected-row token |
| 次要按钮灰底 | `secondaryVariant` | `#FFF0F0F0` / `#FF434343` | Default `ButtonDefaults.buttonColors()` container |
| 输入框灰底 | `secondaryContainer` | `#FFF0F0F0` / `#FF434343` | Default `TextFieldDefaults.textFieldColors()` container |
| 下拉选中浅蓝/深色底 | `tertiaryContainer` / `onTertiaryContainer` | `#FFEAF2FF` / `#FF2B3B54` | Dropdown selected item + content |
| 红色错误 / 危险 | `error` / `errorContainer` / `onErrorContainer` | `#FFE94634` / `#FFFDF6F4` | No built-in `warning`, `success`, `alert`, or `info` token |
| 黄色警告/成功/信息 | App semantic token | — | Define explicitly; do not reuse `error` |
| 页面画布 / 普通表面 | `background` (`onBackground`), `surface` (`onSurface`) | `#FFFFFFFF`/`#FF242424`, `#FFF7F7F7`/black | Generic canvas/surface |
| Card / 分组容器底 | `surfaceContainer` | `#FFFFFFFF` / `#FF242424` | `CardDefaults.defaultColors()` background |
| SearchBar 输入胶囊 | `InputField.color` | `#FFE8E8E8` / `#FF242424` | `surfaceContainerHigh`; `SearchBar` wrapper has no `color` |
| 摘要文字 / 箭头图标 | `onSurfaceVariantSummary` / `onSurfaceVariantActions` | `#99000000` / `#66000000` (light) | Summary vs action affordance |
| 边框 / 分割线 / 遮罩 | `outline` / `dividerLine` / `windowDimming` | `#FFD9D9D9` / `#FFE0E0E0` / `#4D000000` | Structural roles |

## Component Color Defaults

- `ButtonDefaults.buttonColorsPrimary()` (`primary`/`onPrimary` + `disabledPrimaryButton`/`disabledOnPrimaryButton`) vs `.buttonColors()` (`secondaryVariant`). `ButtonDefaults` also owns `TextButton` colors: `textButtonColors()` and `textButtonColorsPrimary()` (there is no `TextButtonDefaults`).
- `CardDefaults.defaultColors()` (`surfaceContainer` + `onSurfaceContainer`). For cards on glass sheets/dialogs, use `GlassCardDefaults.color()` / `.colors()` (translucent fill with dynamic-color contrast).
- `BasicComponentDefaults.titleColor()` (`onBackground`) / `summaryColor()` (`onSurfaceVariantSummary`).
- `SwitchDefaults.switchColors()`: checked `primary`/`onPrimary`, unchecked `secondary`/`onSecondary`; **dynamic-color exception**: unchecked thumb uses `onSurface.copy(0.38f)` and disabled checked thumb uses `surface`.
- `CheckboxDefaults.checkboxColors()`: checked `primary`/`onPrimary`, unchecked `secondary`/`secondary`.
- `RadioButtonDefaults.radioButtonColors()`: selected `primary`; `RadioButtonPreferenceDefaults.radioButtonPreferenceColors()` uses `primary` for selected title/summary while inner radio glyph keeps `RadioButtonColors`.
- `SliderDefaults.sliderColors()`: foreground `primary`, background `sliderBackground`, thumb `onPrimary`.
- `TabRowDefaults.tabRowColors()`: unselected `surface`/`onSurfaceVariantSummary`, selected `surfaceContainer`/`onBackground`.
- `SnackbarDefaults.snackbarColors()`: container `onSecondaryVariant` + content `secondaryVariant`, pill `primary`/`onPrimary`, dismiss `onSurfaceContainerVariant`.
- `BadgeDefaults`: `error` / `onError`. `TooltipDefaults`: plain uses `onSecondaryVariant`/`secondaryVariant`; rich uses `surfaceContainer`/`onSurfaceContainerVariant`/`onSurfaceContainer`/`primary`.
- `FloatingActionButton`: container defaults to `primary`, but inner `Surface` defaults content to `onSurface` — do **not** assume automatic `onPrimary`.
- `ProgressIndicatorDefaults.progressIndicatorColors()`: `primary`, `disabledPrimarySlider`, track `secondaryContainer`.
- `ScrollBarDefaults.scrollBarColors()`: `Color.Unspecified` resolves `onSurface` thumb and hides track.
- `BreadcrumbBarDefaults.breadcrumbBarColors()`: `onBackground.copy(.55f)`, `primary`, `onSurfaceVariantActions`. `DividerDefaults.DividerColor`: `dividerLine`. `DialogDefaults`: `onBackground`, `onSurfaceSecondary`, `background`, `windowDimming`.

## Core Color Roles

In `miuix-ui/.../theme/Colors.kt` (no `warning`/`info`/`success`/`blue`/`gray` properties):
- **Primary**: `primary`, `onPrimary`, `primaryVariant`, `onPrimaryVariant`, `primaryContainer`, `onPrimaryContainer`
- **Secondary**: `secondary`, `onSecondary`, `secondaryVariant`, `onSecondaryVariant`, `secondaryContainer`, `onSecondaryContainer`, `secondaryContainerVariant`, `onSecondaryContainerVariant`
- **Tertiary**: `tertiaryContainer`, `onTertiaryContainer`, `tertiaryContainerVariant`
- **Error**: `error`, `onError`, `errorContainer`, `onErrorContainer`
- **Surfaces & Text**: `background`, `onBackground`, `onBackgroundVariant`, `surface`, `onSurface`, `surfaceVariant`, `onSurfaceSecondary`, `onSurfaceVariantSummary`, `onSurfaceVariantActions`, `disabledOnSurface`
- **Containers**: `surfaceContainer`, `onSurfaceContainer`, `onSurfaceContainerVariant`, `surfaceContainerHigh`, `onSurfaceContainerHigh`, `surfaceContainerHighest`, `onSurfaceContainerHighest`
- **Structural & Slider**: `outline`, `dividerLine`, `windowDimming`, `disabledPrimary*`, `disabledSecondary*`, `sliderKeyPoint`, `sliderKeyPointForeground`, `sliderBackground`
