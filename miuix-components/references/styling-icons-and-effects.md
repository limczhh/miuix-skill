# Styling, Icons, and Effects

Use this reference only for icon dependencies, blur, squircle, material/render primitives, and platform-specific visual effects. Read the relevant topic in this file; do not load the broader design or source-verification references unless the task contains that separate question.

## Current source-audit guides

| Topic | Upstream Markdown guide | Key source files |
|-------|-----------------------|------------------|
| Icons | `docs/guide/icons.md` | `miuix-core/.../icon/MiuixIcons.kt`, `miuix-ui/.../icon/basic/`, `miuix-icons/.../extended/`, `miuix-glass-icons/.../icon/glass/` |
| Colors | `docs/guide/colors.md` | `miuix-ui/.../theme/Colors.kt` |
| Theme + ThemeController | `docs/guide/theme.md` | `miuix-ui/.../theme/MiuixTheme.kt`, `miuix-ui/.../theme/ThemeController.kt` |
| Text styles | `docs/guide/textstyles.md` | `miuix-ui/.../theme/TextStyles.kt` |
| Utilities | `docs/guide/utils.md` | `miuix-ui/.../utils/` |
| Blur | `docs/guide/blur.md` | `miuix-blur/src/commonMain/kotlin/` |
| Material/render primitives | `docs/guide/material.md` | `miuix-material/src/commonMain/kotlin/top/yukonga/miuix/kmp/material/` |
| Glass surfaces and controls | `docs/guide/glass.md` and [Glass components](glass.md) | `miuix-glass/src/commonMain/kotlin/top/yukonga/miuix/kmp/glass/` |
| Squircle | `docs/guide/squircle.md` | `miuix-squircle/src/commonMain/kotlin/` |
| Multiplatform behavior | `docs/guide/multiplatform.md` | Platform source sets named by the selected API |

Do not use the unversioned rendered docs site or generated online Dokka to infer a signature. Read the current pinned Kotlin source when a guide does not show the exact contract, and check the target project's published version separately.

## Icons

- `miuix-ui` includes the seven basic Regular-weight icons: `ArrowRight`, `ArrowUpDown`, `Check`, `Close`, `Search`, `SearchCleanup`, and `Sidebar`.
- Extended icons require `top.yukonga.miuix.kmp:miuix-icons:0.9.5-beta01` for commonMain or `top.yukonga.miuix.kmp:miuix-icons-android:0.9.5-beta01` for Android-only projects.
- Glass icons require the separate `miuix-glass-icons` dependency and are exposed through `MiuixIcons.Glass`. The source package is `top.yukonga.miuix.kmp.icon.glass`; import the requested extension from that package after confirming the icon name exists.
- Verify every requested name in `docs/guide/icons.md` and its source file. Use `MiuixIcons.Name` for Regular; use `MiuixIcons.Light.Name`, `MiuixIcons.Normal.Name`, `MiuixIcons.Medium.Name`, or `MiuixIcons.Demibold.Name` only when that weight exists in the pinned source.
- Prefer an existing semantic icon. Do not invent a Material icon name or add a custom vector before checking the basic and extended sets.

### Icon placement decision

| Placement | Use | Rule |
|---|---|---|
| Compact independent action, toolbar action, or clickable glyph | `IconButton { Icon(...) }` | Do not put `clickable` directly on `Icon`; `IconButton` owns button semantics and a default 40 dp minimum target |
| Leading decoration in a setting/content row | The row's `startAction` or other dedicated leading slot | `BasicComponent` has `startAction`, not a generic `icon` parameter; let the row own alignment and disabled behavior where supported |
| TextField decoration or navigation item | The component's `leadingIcon`/`trailingIcon`/`icon` slot | Prefer the semantic slot over rebuilding the component with a custom `Row` |
| Inline icon + text with no owning slot | Compose `Row(verticalAlignment = Alignment.CenterVertically)` | Add explicit spacing, keep both under the surrounding content color, and preserve text wrapping |

`Icon` uses the vector/painter's intrinsic size and falls back to 24 dp when none exists; `IconButton` supplies the touch target, not the glyph size. Use a localized `contentDescription` for a meaningful image/action and `null` only when the icon is purely decorative or its adjacent text already provides the semantics.

## Blur

- Add `miuix-blur` explicitly; it is not included by `miuix-ui`.
- On Android, blur requires API 33. Gate shader-backed behavior with the public support check shown by the pinned guide and Example, and preserve a readable non-blurred surface when unsupported.
- Treat Example blur backdrops and bar helpers as application-specific composition. Copy only the effect and fallback the product actually needs.
- Import `isRuntimeShaderSupported`, `RuntimeShader`, `RuntimeShaderCache`, `DefaultRuntimeShaderCache` and `LocalRuntimeShaderCache` from `top.yukonga.miuix.kmp.material`; `DeviceTilt` and `rememberDeviceTilt` come from `material.sensor`. `Backdrop` recording/downscale values use `Float`, and `BlurDefaults.MaxBlurRadius` is `100f`.

### Glass and material boundary

- `miuix-material` supplies `RuntimeShader`, `GlassStyle`, `GlassMaterial`, `GlassShape`, `GlassShadow`, `GlassDefaults`, and edge-stroke definitions.
- `miuix-glass` composes those definitions into `glassPanel`, `glass`, `glassShadow`, `glassSurface`, Glass app bars, navigation, tabs, buttons, popups, dialogs, sheets, and `GlassDropdownPreference`.
- A Glass component that accepts `Backdrop?` samples recorded content; create it with `rememberLayerBackdrop` and apply `Modifier.layerBackdrop` to an opaque background layer that excludes the Glass surface. A `null` backdrop is an explicit opaque fallback, not an error.
- `GlassDialog`, `GlassBottomSheet`, and `GlassDropdownPreference` use the Miuix `Scaffold` popup host. Keep them composed while they exit and keep the popup host/scrim out of the backdrop recording. `GlassPopup`, `GlassDropdownPopup`, `GlassTransformPopup`, `GlassSecondaryPopup`, and `GlassIconDropdownMenu` are `BoxScope` components and must share a root box with their anchor. Use [Glass components](glass.md) for the exact host and callback contract.

For an ordinary blurred rim, select `EdgeStrokes`; Glass presets use `GlassEdgeStrokes`. Both use `EdgeStroke` / `EdgeLightSource`. Draw the stroke separately from backdrop effects, using the same shape:

```kotlin
import top.yukonga.miuix.kmp.blur.blur
import top.yukonga.miuix.kmp.blur.drawBackdrop
import top.yukonga.miuix.kmp.material.EdgeStrokes
import top.yukonga.miuix.kmp.material.edgeStroke

Modifier
    .drawBackdrop(backdrop = backdrop, shape = { shape }, effects = { blur(20f) })
    .edgeStroke(shape = shape, stroke = EdgeStrokes.MiddleLight)
```

`material.rememberTiltLight(base: EdgeLightSource, sensitivity: Float = 0.1f)` returns an `EdgeLightSource`. Use the theme-appropriate preset or verified custom light data; these optical values do not belong in per-page copies.

### Progressive blur

The current pinned source provides `ProgressiveBlur` and `Modifier.progressiveTextureBlur`. Use `ProgressiveBlur.Top`, `.Bottom`, `.Left`, or `.Right` for edge fades, or copy a preset and tune `startFraction`, `endFraction`, and positive `curve`. `colors` and `noiseCoefficient` fade with the blur; progressive noise defaults to `0f`.

Use progressive blur for navigation bars and narrow edge bands. Its pixel-sharp clear end adds a full-resolution overlay pass, so it is more expensive than a uniform `textureBlur` over the same area. In a custom `drawBackdrop` pipeline, call `progressiveBlur(...)` or `progressiveTextureBlurEffect(...)` in the effect block and pass the same `ProgressiveBlur` as `drawBackdrop(progressiveGradient = ...)`; the convenience modifier wires both sides together. Keep the progressive noise default (`BlurDefaults.ProgressiveNoiseCoefficient = 0f`) unless a grain effect is deliberate.

## Squircle

- `miuix-squircle` is already brought in by `miuix-ui`; add it explicitly only when using it without `miuix-ui`.
- Use the public modifiers `Modifier.squircleBackground`, `squircleClip`, `squircleSurface`, and `squircleBorder`. Do not invent a `SquircleShape` API.
- Shader-backed modifiers fall back to matching rounded-corner rendering below Android API 33. Keep the same corner parameters so the fallback preserves geometry.
- Prefer component Defaults before applying a custom squircle modifier. Add custom surface, clipping, or border behavior only for an explicit visual requirement.
