# Custom Blur, Material, Squircle, and Icons

Use for icons (`MiuixIcons.*`), low-level `miuix-blur` / `miuix-material` rendering pipelines, `EdgeStroke` / `TiltLight`, and `miuix-squircle` continuous corners at pinned `0.9.5-rc01`.

Contents: [Icons](#icons) · [Custom blur pipelines](#custom-blur-pipelines) · [Material & edge strokes](#material-and-edge-strokes) · [Squircle](#squircle)

## Icons

Guide: `docs/guide/icons.md`; sources: `miuix-core/.../icon/MiuixIcons.kt`, `miuix-ui/.../icon/basic/`, `miuix-icons/.../extended/`, `miuix-glass-icons/.../icon/glass/`.

- **Extension-property imports (avoid `receiver type mismatch`)**: `MiuixIcons` lives in `top.yukonga.miuix.kmp.icon`, while individual icon glyphs are **extension `val`s** in subpackages. Always import **both** `MiuixIcons` and each icon property from its package (e.g., `import top.yukonga.miuix.kmp.icon.basic.Check`, not `MiuixIcons` alone!).
- **Basic (included in `miuix-ui`)**: `MiuixIcons.Basic` exports **only 7 icons** (`ArrowRight`, `ArrowUpDown`, `Check`, `Close`, `Search`, `SearchCleanup`, `Sidebar`) in `top.yukonga.miuix.kmp.icon.basic.*`. Notice **`MiuixIcons.Basic` has NO `Back` or `Sort` icon** — if optional `miuix-icons` is not declared, mirror `MiuixIcons.Basic.ArrowRight` (`scaleX = -1f` in LTR) for Back and use `MiuixIcons.Basic.ArrowUpDown` for Sort.
- **Extended (`miuix-icons` / `miuix-icons-android`)**: Optional artifact exposing `MiuixIcons.<Name>` (e.g. `Back`, `Sort`, `Settings`, `ChevronBackward` via `top.yukonga.miuix.kmp.icon.extended.*`). `MiuixIcons.Light.<Name>`, `.Normal`, `.Medium`, `.Demibold` exist only where defined in `docs/guide/icons.md` / source.
- **Glass (`miuix-glass-icons`)**: Separate artifact exposing `MiuixIcons.Glass` (`top.yukonga.miuix.kmp.icon.glass.*`). Note `rc01` renames: `XiaominListen` -> **`XiaomiListen`**, `CeditSelection` -> **`CheckSelection`**.
- **Placement**: Wrap clickable icons in `IconButton` (40 dp target; do not apply `.clickable` to `Icon`). `Icon` uses painter intrinsic size (24 dp fallback). Pass `contentDescription = null` for purely decorative glyphs beside text.

## Custom Blur Pipelines

Guide: `docs/guide/blur.md`; source: `miuix-blur/.../blur/`.

- **Unit distinction**: `Modifier.textureBlur`, `textureEffect`, `progressiveTextureBlur`, and `BackdropEffectScope.textureBlurEffect` / `progressiveTextureBlurEffect` take **dp**; inside `drawBackdrop(effects = { ... })`, `BackdropEffectScope.blur` / `progressiveBlur` take **pixels** (e.g., `20f * density`). `BlurDefaults.MaxBlurRadius` is `100f`.
- **Bypass vs zero**: Control `drawBackdrop(enabled = ...)` to skip the pipeline cleanly (setting `blurRadius = 0f` still runs blends/processing; setting `alpha = 0f` does not disable blur). `rc01` `miuix-blur` skips zero-blur layers internally, specializes 1D blur, exposes `Backdrop.backdropCoordinates` + `BackdropEffectScope.buildRuntimeShaderEffect`, clamps padded bounds, and reuses `GraphicsLayer` record blocks.
- **Progressive blur cost**: `ProgressiveBlur.Top`/`.Bottom`/`.Left`/`.Right` (`ProgressiveNoiseCoefficient = 0f`) renders a full-resolution clear pass alongside the blur — restrict it to bars/edge bands rather than large cards.
- **Foreground vs sampled blur**: Default blur keeps foreground sharp; `contentBlendMode = BlendMode.DstIn` masks sampled blur with content alpha. Compose `Modifier.blur` blurs the node itself, not a recorded backdrop.

## Material and Edge Strokes

Guide: `docs/guide/material.md`; sources: `miuix-material/.../material/`.

- Capability & cache imports (`top.yukonga.miuix.kmp.material`): `isRuntimeShaderSupported`, `isRenderEffectSupported`, `RuntimeShader`, `RuntimeShaderCache`, `DefaultRuntimeShaderCache`, `LocalRuntimeShaderCache`, `ShaderBrushCache`, `isRuntimeShaderBrushImmutable`. `DeviceTilt` and `rememberDeviceTilt` live in `top.yukonga.miuix.kmp.material.sensor`.
- **`EdgeStroke` vs `Glass`**: Select `EdgeStrokes` for ordinary blurred rims and `GlassEdgeStrokes` for Glass panels. Draw strokes separately from `drawBackdrop` using the matching shape. `drawEdgeStroke` is skipped cleanly when runtime shaders are unsupported.
- **Tilt-driven lights (avoid 50Hz recomposition)**:

```kotlin
val primaryLight by rememberTiltLightState(
    base = EdgeLightSource(0.5f, 0.5f, -0.5f, intensity = 0.4f),
    sensitivity = 0.1f, // UV shift per radian
)

Modifier
    .drawBackdrop(backdrop = backdrop, shape = { shape }, effects = { blur(20f * density) })
    .edgeStroke(shape = shape, stroke = { EdgeStrokes.MiddleLight.copy(primary = primaryLight) })
```

  `Modifier.glass(..., stroke = { ... })` and `Modifier.glassPanel(..., stroke = { ... })` also read stroke providers in the draw phase without triggering recomposition or rebuilding blur pipelines. (`rememberTiltLight` remains available if a composition-time value is specifically required.)

## Squircle

Guide: `docs/guide/squircle.md`; source: `miuix-squircle/.../squircle/`. Already included transitively by `miuix-ui`.

- **Public modifiers**: `Modifier.squircleBackground`, `squircleClip`, `squircleSurface` (single offscreen layer for fill + child clip), `squircleBorder` (path-based, includes deferred `width: () -> Dp` & `color: () -> Color` overloads), plus layout-direction-agnostic **`absoluteSquircleBackground`**, **`absoluteSquircleSurface`**, **`absoluteSquircleClip`** (`topLeft`, `topRight`, `bottomRight`, `bottomLeft`). There is **no** `SquircleShape` class.
- **Low-level Path**: `Path.addSquircleRect` provides both uniform `cornerRadius: Float` and per-corner `topLeft, topRight, bottomRight, bottomLeft: Float` pixel overloads (`extension` clamped to `[1.0, 2.0]`, default `SquircleDefaults.Extension = 1.1f`, `squircleEnabled` flag).
- **Fallbacks & fast paths (`rc01`)**:
  - Below Android API 33 or when `LocalSquircleEnabled` is `false` (`isSquircleEnabled()` returns `false`), all squircle modifiers fall back automatically to matching `RoundedCornerShape` geometry.
  - Pure capsules (corners >= half of shorter side) bypass offscreen layers using hardware `CircleShape`; oversized nodes (either side `> 4096 px` or area `> 4096 × 2160`) fall back to cached `Path` clipping.
