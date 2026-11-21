# Blur Controls and Fallbacks

Use when implementing a blur setting, reviewing effects-off behavior, or diagnosing why a component still looks blurred. Evidence is pinned to Miuix **0.9.5-beta03**, commit `3526cfe2ca0779c5a2eab99af8737a96c9a8a885`. These are library facts and Example integration choices, not additional visual requirements.

Contents: [controls](#separate-the-controls) · [behavior matrix](#component-behavior) · [transitions](#foreground-blur-is-a-separate-control) · [custom effects](#custom-effect-boundaries) · [tabs](#disable-tab-material-explicitly) · [inheritance](#resolve-inherited-sampling) · [verification](#verification-and-source-routes).

## Separate the controls

There is no universal library `enableBlur` switch. Example's `AppState.enableBlur` is application-owned and is translated into different arguments at each component boundary. Keep these decisions distinct:

- **Runtime support:** `material.isRuntimeShaderSupported()` gates the backdrop/texture pipeline (Android API 33+). `isRenderEffectSupported()` checks a different capability (Android API 31+); it does not establish backdrop-blur support. See [platform setup](setup-and-theme.md#quick-start).
- **Background sampling:** supply/remove the effective backdrop, or use the effect's `enabled` parameter. A remembered backdrop is not proof that a scene is recorded. An empty recording is not an off switch.
- **Progressive background band:** `GlassTopAppBar.progressiveBlur` chooses that band, not every effect in the bar.
- **Foreground transition blur:** Glass titles, navigation visibility and popup morph/exit effects have separate controls; see [transition boundaries](#foreground-blur-is-a-separate-control).
- **Decoration:** strokes, shadows, fills and opacity have their own policies. Disabling sampling does not automatically remove these or change component family.

Define whether a product setting means “background blur” or “all blur effects.” Beta03 Glass popups and navigation visibility do not expose a universal switch for their transition blur; `backdrop = null` cannot promise the latter meaning.

## Component behavior

“Off” below means application-disabled background sampling on a shader-capable runtime. Unsupported-runtime behavior is listed separately because it can preserve different decoration. Default colors assume the supplied theme colors remain opaque where intended.

| Component / effect | Sampling enabled | Application turns sampling off | Runtime shaders unavailable |
|---|---|---|---|
| Ordinary `TopAppBar` / `SmallTopAppBar`, `NavigationBar` | Application wrapper adds `textureBlur`; foreground bar is transparent | Remove/disable wrapper effect and restore semantic `surface` background | Same readable ordinary surface; the library bar does not add its own blur |
| Ordinary `FloatingNavigationBar` | Example adds texture blur **and** `edgeStroke`, using a transparent bar | Example removes both modifiers and uses `surfaceContainer` | Same ordinary fill. Retaining a decorative stroke instead is an explicit application choice |
| Ordinary `Card`, `IconButton`, `FloatingActionButton`, `FloatingToolbar`, `NavigationRail`, `TabRow`, `TabRowWithContour` | No built-in backdrop sampler; application decoration supplies any sampling | Keep the ordinary surface/state, or gate the application effect | Ordinary rendering remains; no automatic Glass material is added |
| Ordinary `SearchBar` / `InputField` | Neither samples a backdrop internally. `InputField(color = Color.Transparent)` permits an application-drawn blurred capsule behind it | Remove the application effect and restore the intended capsule color (`surfaceContainerHigh` by default); preserve query/focus/expanded state | Same ordinary capsule policy; transparency alone supplies no fallback |
| `OverlayDialog`, `OverlayBottomSheet`, `WindowDialog`, `WindowBottomSheet` | Default surfaces do not sample a backdrop. Overlay variants can use typed Glass surface adapters | Keep ordinary surface, or gate the adapter. Window variants lack those typed surface slots | Ordinary surface remains; a selected Glass adapter follows its own fallback |
| `textureBlur`, `textureEffect`, `progressiveTextureBlur`, `drawBackdrop` | Sample a separately recorded backdrop and apply the pipeline | `enabled = false` bypasses the effect; caller supplies any required fallback fill | Effect bypassed; no automatic opaque panel is created |
| `GlassTopAppBar` | Non-null supported backdrop feeds action/tab material; `progressiveBlur = true` adds a progressive band | Null effective backdrop removes sampled material; no progressive band means the scroll-driven `bandBrush` gradient remains. Title blur remains independently configured | Uses the non-progressive gradient path and child fallbacks; does not become an ordinary solid app bar |
| `GlassSearchOverlay` | Samples the recorded page for its progressive header and glass search/cancel pills when enabled | Null/unsupported sampling retains the full-screen search geometry with an opaque fill; query, focus and Back/Escape ownership remain | The overlay stays an in-page owner and remains composed through exit; it is not a route or a generic blurred Box |
| `GlassIconButton` | Samples its resolved backdrop and follows inherited top-bar material progress | With no effective backdrop, uses `fill` as its material appears; retains press response, shadow and supported stroke | Fill/press behavior remains; shader stroke is skipped and shadow has a non-shader fallback |
| `GlassFloatingActionButton` / `GlassFloatingToolbar` | Own sampled material and decoration | Null backdrop uses `fill` (FAB default: primary); geometry, actions and supported rim/shadow remain | Fill and shadow fallback remain; no shader bloom rim |
| `GlassNavigationBar` / `GlassNavigationRail` | Own sampled panel and navigation interaction | Null backdrop uses `surfaceContainer`; selection, morph/expansion and supported decoration remain. The bar's `visible` transition can still blur the whole group | Panel fill and shadow fallback remain; no shader bloom rim. Visibility-transition blur uses a separate capability |
| `GlassTabRow` | Each tab uses material and selected/resting color ramps | `surfaceAlpha = 0f` disables the tab material pipeline and restores the resting tab visuals; selected color, label, semantics and press behavior remain. A null effective backdrop also removes the sampling input | No sampled material; resting rim/shadow behavior follows its own code and colors, not an identical Glass rendering |
| `GlassSegmentedTabRow` | Track material fades in with `surfaceAlpha`; indicator and interaction are separate | Keep required non-null backdrop; set `surfaceAlpha = 0f` to disable its material and restore the default opaque resting track. Indicator, drag and press remain; the material stroke is disabled too | Internally forces the material ramp to zero, retaining track/indicator and fallback shadow |
| `GlassDialog`, `GlassBottomSheet`, typed surface helpers | **Both support background blur** with a valid recorded backdrop and shader support; sample the scrim-adjusted page scene while foreground content stays sharp | Null backdrop gives `containerColor` (default: theme `background`); host, exit animation and sheet drag remain | Same fill fallback; shader stroke absent. Sheet overscroll extension omits stroke even with effects on |
| `GlassPopup`, `GlassDropdownPopup`, `GlassTransformPopup`, `GlassSecondaryPopup`, `GlassIconDropdownMenu`, `GlassDropdownPreference` popup, `glassSurface` | Use the resolved scene and popup material | No resolved backdrop gives `visuals.containerColor`; supported stroke can remain. Morph/exit foreground blur is independent | Container fill remains; shader stroke absent. Do not infer transition-effect support solely from the shader check |
| `GlassPopupItem`, `GlassDialogButton` | No independent background sampler | Row/button colors and interaction remain; enclosing popup/dialog owns material | Same ownership; these are not separately blurred panels |
| `GlassTextField` | Delegates to `TextField` with translucent Glass colors | State, editing, line limits and IME behavior remain; only field visual defaults change | Use the state-based overload; deprecated value overloads do not change the hosting/validation contract |
| Custom `glassPanel` / low-level `glass` | Apply selected Glass pipeline | `glassPanel(backdrop = null)` uses caller `fallback`, or only clips if omitted. `glass(enabled = false)` bypasses the effect | `glassPanel` takes fallback; `glass` bypasses. Neither invents an opaque fill |

`blurRadius = 0f` removes the blur radius, not color blending/noise or backdrop processing. `alpha = 0f` is not a general effect bypass. In beta03 both tab components use **`surfaceAlpha`** as a material/resting-state ramp and gate their material pipeline when the ramp reaches the off endpoint. `GlassTabRow` also accepts a nullable backdrop, while `GlassSegmentedTabRow` still requires a non-null backdrop. Do not generalize this tab-specific contract to other controls.

## Foreground blur is a separate control

| Source boundary | Foreground effect | Relationship to sampling off |
|---|---|---|
| Ordinary `TopAppBar` / `SmallTopAppBar` | Ordinary top-bar title path uses zero title-blur radius | Turning on an external background effect does not turn on Glass title transitions |
| `GlassTopAppBar` | Small/large title and subtitle transition blur | `backdrop = null` and `progressiveBlur = false` do not disable it; use `titleBlurRadius = 0.dp` when required |
| `GlassNavigationBar` | Changing `visible` shrinks/fades/blurs the navigation group, including its trailing action, using a `graphicsLayer` `BlurEffect` | Independent of backdrop and material. `visible` controls the whole transition; beta03 has no separate public visibility-blur radius/switch. Do not claim null backdrop disables all blur |
| `GlassPopup`, `GlassDropdownPopup`, `GlassTransformPopup` | Internal `BlurEffect` softens content during morph/exit; transform also handles copied anchor content | Null backdrop changes the background material, not these effects. Composed menus/preferences inherit the renderer's behavior |

The latter foreground effects use Compose/RenderEffect paths rather than the sampled-backdrop pipeline. Their platform support must be considered separately from `isRuntimeShaderSupported()`. Keep the product setting's scope explicit; do not invent an unsupported all-effects-off parameter.

## Custom effect boundaries

`textureBlur`, `textureEffect` and `progressiveTextureBlur` use radii in **dp** and convert by density. In a custom `drawBackdrop` block, `BackdropEffectScope.blur` / `progressiveBlur` take radii in **pixels**; `textureBlurEffect` / `progressiveTextureBlurEffect` take **dp**. These scope functions configure part of a pipeline rather than supplying an independent component fallback. To bypass the entire pipeline, control the owning `drawBackdrop(enabled = ...)` instead of merely setting one radius to zero.

Default texture blur leaves foreground content sharp. `contentBlendMode = BlendMode.DstIn` instead masks the sampled result with content alpha, enabling a foreground-blur treatment. Compose `Modifier.blur` blurs its own content and is a different operation. Preserve this distinction when adapting examples; a foreground blur is not a replacement for recording the background.

## Disable tab material explicitly

The beta03 `GlassTabRow` and `GlassSegmentedTabRow` code compute their material ramp from shader support and `surfaceAlpha`; the ramp's zero endpoint gates the material pipeline while retaining the row's resting visuals. `GlassTabRow` accepts a nullable backdrop, whereas the segmented row still requires a non-null backdrop. For either row, an unrecorded dummy backdrop is not the policy switch; pass the intended `surfaceAlpha` and resolve the backdrop according to the component contract.

The pinned [Example IconPage](https://github.com/compose-miuix-ui/miuix/blob/3526cfe2ca0779c5a2eab99af8737a96c9a8a885/example/shared/src/commonMain/kotlin/IconPage.kt) demonstrates material gating through `surfaceAlpha`. Adapt that gating to the application's coordinated selection:

```kotlin
val samplingActive = backgroundBlurEnabled && isRuntimeShaderSupported()

GlassSegmentedTabRow(
    tabs = tabs,
    selectedIndex = selection.selectedIndex,
    onSelect = selection::select,
    backdrop = sceneBackdrop,
    surfaceAlpha = if (samplingActive) 1f else 0f,
)
```

`sceneBackdrop`, the setting and `selection` are application-owned; `selection` follows the [tab/pager coordination pattern](../../miuix-guidelines/references/navigation-compositions.md#tab-selection-and-page-switching), not a Miuix type. Apply the same `surfaceAlpha` gate to `GlassTabRow` when using that variant; its `backdrop` may be nullable, while the segmented row's remains required. The backdrop can stay remembered; record the intended opaque scene when sampling is active. A custom `trackColor` must preserve readability at the resting endpoint. If a top bar supplies its own material ramp, combine it with the setting so the off branch remains zero. Keep selection/state above presentation branches rather than replacing tabs just to turn material off.

## Resolve inherited sampling

Passing null is effective only after resolving context and anchors:

- `GlassIconButton` can take the top bar's `buttonBackdrop` even when its own argument is null. `inheritTopBarMaterial = false` changes material preference but still permits backdrop fallback to that context. Gate sampling at the owning bar/context, not only the child call.
- Both Glass tab variants prefer the top bar context's backdrop over their argument; coordinate the parent and the tab material ramp.
- `GlassTransformPopup` falls back to `anchor.surface.backdrop`; `GlassSecondaryPopup` can use its `materialAnchor` backdrop. Disable the anchor's sampling as well when turning sampling off for the entire flow. Cascading menus also have a secondary scene input.

Do not clear selection, recreate a popup host or interrupt dismissal merely to switch effects. Keep the existing state/lifetime owner and change the matching visual inputs.

## Verification and source routes

Read only the source family needed for the change, under the pinned commit:

| Boundary | Source files |
|---|---|
| Ordinary bar integration and application setting | `example/shared/src/commonMain/kotlin/utils/PageUtils.kt`, `AppContent.kt`; tab integration: `IconPage.kt` |
| Blur bypass, zero radius, recording | `miuix-blur/.../blur/TextureEffect.kt`, `DrawBackdropModifier.kt`, `LayerBackdrop.kt`, `BackdropEffects.kt` |
| Bar band/title, inherited actions/tabs | `miuix-glass/.../glass/GlassTopAppBar.kt`, `GlassIconButton.kt`, `GlassTabRow.kt`; navigation visibility: `GlassNavigationBar.kt` |
| Ordinary surfaces and adapters | `miuix-ui/.../basic/` files named for the ordinary controls above (search input: `SearchBar.kt`); `overlay/OverlayDialog.kt`, `overlay/OverlayBottomSheet.kt`, `window/WindowDialog.kt`, `window/WindowBottomSheet.kt` |
| Panel and control fallback | `GlassPanel.kt`, `Glass.kt`, `GlassFloatingActionButton.kt`, `GlassFloatingToolbar.kt`, `GlassNavigationBar.kt`, `GlassNavigationRail.kt` in the same Glass package |
| Overlay and popup resolution | `GlassDialogSurface.kt`, `GlassBottomSheetSurface.kt`, `GlassPopupSurface.kt`, `GlassTransformPopup.kt`, `GlassSecondaryPopup.kt`, `GlassIconDropdownMenu.kt`, `GlassDropdownPreference.kt` in the same package |
| Rim and shadow capability | `miuix-material/.../material/internal/EdgeStrokeDrawing.kt`; `miuix-glass/.../glass/Glass.kt` |

Coverage is the beta03 component sampling/fallback branches, the foreground blur entry points in `miuix-ui` / `miuix-glass`, and the custom-pipeline boundaries above. Ordinary components without a sampler are distinguished from Glass containers and their non-sampling child rows. Shader formulas, every effect-helper overload and application-specific effects remain source-routed rather than copied here.

Source inspection establishes branch behavior, not a device screenshot or performance result. Check enabled/disabled sampling **on a supported runtime**, unsupported runtime separately, light/dark themes, scrolling/resting states and popup enter/exit. A disabled-setting test on a supported device does not prove the unsupported path.
