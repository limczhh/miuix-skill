# Styling, Icons, and Effects

Use this reference for styling resources that have dependency or platform consequences. Read every guide as a Markdown file from tag `v0.9.3` through [Source verification](source-verification.md). For semantic colors, typography, grouping, and customization boundaries, continue with [Design language and Defaults](design-language.md).

## Version-Pinned Guides

| Topic | Tagged Markdown guide | Key source files |
|-------|-----------------------|------------------|
| Icons | `docs/guide/icons.md` | `miuix-core/.../icon/MiuixIcons.kt`, `miuix-ui/.../icon/basic/`, `miuix-icons/.../extended/` |
| Colors | `docs/guide/colors.md` | `miuix-ui/.../theme/Colors.kt` |
| Theme + ThemeController | `docs/guide/theme.md` | `miuix-ui/.../theme/MiuixTheme.kt`, `miuix-ui/.../theme/ThemeController.kt` |
| Text styles | `docs/guide/textstyles.md` | `miuix-ui/.../theme/TextStyles.kt` |
| Utilities | `docs/guide/utils.md` | `miuix-ui/.../utils/` |
| Blur | `docs/guide/blur.md` | `miuix-blur/src/commonMain/kotlin/` |
| Squircle | `docs/guide/squircle.md` | `miuix-squircle/src/commonMain/kotlin/` |
| Multiplatform behavior | `docs/guide/multiplatform.md` | Platform source sets named by the selected API |

Do not use the unversioned rendered docs site or generated online Dokka to infer a `v0.9.3` signature. Read the tagged Kotlin source when a guide does not show the exact contract.

## Icons

- `miuix-ui` includes the seven basic Regular-weight icons: `ArrowRight`, `ArrowUpDown`, `Check`, `Close`, `Search`, `SearchCleanup`, and `Sidebar`.
- Extended icons require an explicit `miuix-icons` dependency: `top.yukonga.miuix.kmp:miuix-icons:0.9.3` for commonMain or `top.yukonga.miuix.kmp:miuix-icons-android:0.9.3` for Android-only projects.
- Verify every requested name in `docs/guide/icons.md` and its source file. Use `MiuixIcons.Name` for Regular; use `MiuixIcons.Light.Name`, `MiuixIcons.Normal.Name`, `MiuixIcons.Medium.Name`, or `MiuixIcons.Demibold.Name` only when that weight exists in tagged source.
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
- On Android, blur requires API 33. Gate shader-backed behavior with the public support check shown by the tagged guide and Example, and preserve a readable non-blurred surface when unsupported.
- Treat Example blur backdrops and bar helpers as application-specific composition. Copy only the effect and fallback the product actually needs.

## Squircle

- `miuix-squircle` is already brought in by `miuix-ui`; add it explicitly only when using it without `miuix-ui`.
- Use the public modifiers `Modifier.squircleBackground`, `squircleClip`, `squircleSurface`, and `squircleBorder`. Do not invent a `SquircleShape` API.
- Shader-backed modifiers fall back to matching rounded-corner rendering below Android API 33. Keep the same corner parameters so the fallback preserves geometry.
- Prefer component Defaults before applying a custom squircle modifier. Add custom surface, clipping, or border behavior only for an explicit visual requirement.
