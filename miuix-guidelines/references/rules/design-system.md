# Design System Rules

Shared visual specification and applicability contract for `references/rules/`. Exact Miuix symbols, Defaults, and version behavior are verified via `miuix-components`. User requirements take precedence.

Contents: [scope](#scope-and-rule-strength) · [exceptions](#exceptions-and-conformance) · [topic routing](#required-topic-rules) · [components](#component-selection-and-defaults) · [theme](#theme-and-system-appearance) · [colors](#surface-and-color-roles) · [typography](#typography-roles) · [shapes](#shapes-and-clipping) · [groups](#grouped-content-cards) · [icons & tags](#icons-images-and-tags) · [language & a11y](#language-and-accessibility) · [customization](#when-customization-is-justified)

## Scope and Rule Strength

- **Greenfield** applies all relevant rules to the new boundary; **Brownfield** applies them inside the touched scope and preserves unrelated conventions; **Mixed** follows the existing shell and applies rules inside the new boundary.
- `MUST` / `DO NOT` and unqualified imperatives are mandatory when their stated condition applies; `SHOULD` / `prefer` are recommendations; `MAY` / `optional` are choices (selecting an optional component activates its rules).
- Only touch sections matching the task (e.g., a color fix need not rewrite navigation).

## Exceptions and Conformance

Deviations require a concrete basis: user requirement, coherent project convention (shared token/helper or consistent surrounding code), accessibility/platform constraint, missing target API, or an unmet need. Record the rule, reason, and replacement behavior when non-obvious (familiarity or one inconsistent literal is not a valid basis). Separate conformance status (`satisfied` / `justified exception` / `unresolved`) from API lookup, compilation, and runtime checks.

## Required Topic Rules

| Task touches | Read matching sections in |
|---|---|
| Host, routes/back, bars, navigation, pager, insets, scroll/refresh, blur, adaptive layout | [Page shell rules](page-shell.md) |
| Preferences, dependent controls, inputs, search, popups, dialogs, sheets, reordering | [Settings & overlay rules](settings-and-overlays.md) |
| State, persistence, async, lazy lists/icons, Snackbar/errors, motion, permissions, import/export | [State & performance rules](state-and-performance.md) |
| Controls, theme, colors, typography, shapes, card metrics, icons/tags, i18n, a11y | Sections below |

## Component Selection and Defaults

- Use the public Miuix component for any interaction it supports; **DO NOT** substitute Material/M3 widgets or rebuild supported controls from raw `clickable` handlers without an unmet requirement.
- Compose `Row`, `Column`, `Box` remain standard arrangement primitives.
- Add an app wrapper only when it owns a real repeated layout, policy, state, or lifecycle contract — never merely to rename a call; prefer thin adapters over copying internal component code.
- Omit visual arguments when stock rendering fits; customize only via verified `*Defaults` / color factories, preserving selected/disabled/pressed/focus states. Never infer a `*Defaults` member from another component.

## Theme and System Appearance

- Wrap routed UI in **one** root `MiuixTheme`; resolve System/Light/Dark/Monet modes and remember `ThemeController` with all changing inputs (including `keyColor`) at the application boundary.
- Observe theme preferences via lifecycle-matched listeners so existing bars/pages update dynamically.
- On Android, enable edge-to-edge at the window boundary and derive status/navigation bar icon appearance from the **resolved app theme** (not system dark mode alone when forced light/dark is active). Match pre-Compose window background to avoid startup flash.
- Keep runtime blur preference/support separate from color mode.

## Surface and Color Roles

- Use component `*Defaults`, `MiuixTheme.colorScheme`, or centralized semantic tokens — **DO NOT** scatter literal `Color(0x...)` or per-page palettes (use `miuix-components` for exact token lookup).
- Pair container colors with matching `on*` roles; use component disabled Defaults rather than arbitrary alpha; keep multicolor brand artwork untinted (`Color.Unspecified`).
- Express error, selection, capability, and progress via text/shape/icon/semantics too, never color alone; tooltips are not the primary error channel.

## Typography Roles

- Let Miuix components own standard typography; explicit text uses `MiuixTheme.textStyles` (`title1..title4`, `headline1..2`, `subtitle`, `main`, `paragraph`, `body1..2`, `button`, `footnote1..2` — never Material `typography` names like `bodyLarge`) or shared app styles rather than ad-hoc numeric literals.
- Titles name the setting/action; summaries explain effect, current value, or prerequisite — avoid repeating identical text.
- Truncated critical text must remain reachable (wrap, expand, detail view, or copy).

## Shapes and Clipping

- Preserve component-owned shapes; do not double-wrap a stock component with redundant background/clip.
- Use squircles for continuous-corner custom surfaces where appropriate; keep `RoundedCornerShape` for blur masks matching rounded specs, media thumbnails, drag surfaces, or capsules. Match blur masks, shadows, and clips to the visible component radius.
- Background painting does not clip children — place clipping on the owning image/interaction boundary rather than adding redundant offscreen clips to fill-only backgrounds. Verify squircle availability/fallback via `miuix-components`.

## Grouped Content Cards

- A labeled semantic group uses `SmallTitle` **outside and immediately before** `Card` (no duplicate gap spacer).
- Grouped `Card` external horizontal padding is **`12.dp`** and external bottom spacing is **`12.dp`** — assign once (never stack `Arrangement.spacedBy` or an extra `Spacer` on top). Keep component `insideMargin` separate from the `12.dp` gutter.
- Keep related items in one `Card` when fixed and small; segment long/dynamic collections in lazy items with separate cards or corner-preserving segmented groups and stable keys. Use `HorizontalDivider`/`VerticalDivider` or whitespace intentionally; nested Cards require genuine containment distinction.

## Icons, Images, and Tags

- Use Miuix icons; secondary-page Back uses localized `contentDescription` and the existing navigator; omit redundant descriptions on decorative icons next to equivalent text.
- Standalone glyph actions use `IconButton` (preserve touch target); application/provider rows use **`40.dp`** icon boxes with **`8.dp`** rounded thumbnails and same-size placeholders (off-path decoding per [State & performance rules](state-and-performance.md#lists-and-image-loading)).
- Compact tags share one reusable component: **`10.dp`** horizontal / **`6.dp`** vertical inner padding, optional **`16.dp`** icon, **`4.dp`** icon-text gap, supporting text role, and press feedback only when interactive.

## Language and Accessibility

- Route all UI strings and meaningful accessibility descriptions through localization resources; resolve strings before `remember` (include in keys) so locale changes never freeze stale labels. Keep storage keys, enums, and route IDs independent of display text; refresh locale-sensitive sorts.
- Preserve semantic roles, enabled/checked/selected state, keyboard focus, usable touch targets, RTL, large font scales, and narrow/short windows; keep actions reachable when text wraps or IME opens. Primary confirm uses primary button roles; destructive actions clarify scope + confirmation.

## When Customization Is Justified

Customize only for supplied brand/visual targets, coherent app conventions, unmet requirements, platform/a11y constraints, or proven defects after fixing ownership. Verify target symbols with `miuix-components` and record any justified deviation.
