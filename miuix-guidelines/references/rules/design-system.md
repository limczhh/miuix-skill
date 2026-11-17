# Design System Rules

This file defines the shared visual specification and the applicability contract for the mandatory rules in this directory. The Skill entrypoint selects a workflow; guides explain design decisions and compositions. Rules define the required result. These are this Skill's requirements, not official restrictions on every Miuix application. Exact API names, imports, modules, Defaults, and version behavior belong to `miuix-components`.

Contents: [scope](#scope-and-rule-strength) · [exceptions](#exceptions-and-conformance) · [topic routing](#required-topic-rules) · [components](#component-selection-and-defaults) · [theme](#theme-and-system-appearance) · [colors](#surface-and-color-roles) · [typography](#typography-roles) · [shapes](#shapes-and-clipping) · [groups](#grouped-content-cards) · [icons and tags](#icons-images-and-tags) · [language](#language-and-resources) · [accessibility](#accessibility-and-content-resilience).

## Scope and rule strength

Read this section, the exception contract, and the topic table before implementation or review. Apply the shared visual sections to the affected controls, surfaces, and text; read the additional rule files whose conditions match the changed boundary. A color-only change does not authorize rebuilding navigation or migrating unrelated settings.

- Greenfield applies all relevant rules to the new boundary.
- Brownfield applies them within the selected migration scope and preserves unrelated code and intentional project contracts.
- Mixed applies the new-boundary rules through the existing shell's contracts.

`MUST` / `DO NOT` express requirements and prohibitions when the stated condition applies. Unqualified imperatives in these rule files have the same force. `SHOULD` / `prefer` express recommendations; `MAY` / `optional` express choices. Choosing an optional component makes its usage requirements applicable.

## Exceptions and conformance

User requirements take precedence. Other deviations need a concrete basis: an intentional coherent project convention, accessibility or platform constraints, an unavailable target API, or a product need the stock component cannot express. Inspect the evidence and proceed; a justified profile exception does not itself require user approval. Record the affected rule, evidence, and replacement behavior when the reason is non-obvious. Familiarity, a copied example, and one inconsistent literal are insufficient.

An established convention needs a shared token/helper, a documented rule, or coherent surrounding usage. Brownfield narrows enforcement scope; it does not make new code exempt. Review applicable requirements as satisfied, justified deviation, or unresolved. Keep this result separate from source verification, compilation, rendered appearance, and device behavior.

## Required topic rules

| Task touches | Mandatory file and sections |
|---|---|
| Page host, route/back, app bars, navigation, pager, insets, scrolling, refresh, blur, or adaptive layout | [Page shell and navigation](page-shell.md); read the matching sections |
| Preferences, dependent controls, inputs, search, dialogs, menus, sheets, selection, or reordering | [Settings and overlays](settings-and-overlays.md); read the matching sections |
| State, persistence, async loading, lists/icons, lifecycle, motion, external actions, permissions, import/export, or operation feedback | [State and performance](state-and-performance.md); read the matching sections |
| Controls, colors, text, surfaces, icons, language, or accessibility | The matching visual sections in this file |

A new settings page usually needs all four files, but only its applicable sections. A local dialog edit need not load unrelated navigation and blur rules. Helper names and code shapes in guides are application patterns, not extra library APIs.

## Component selection and Defaults

- Use the public Miuix component for an interaction it supports. DO NOT substitute a familiar Material/Material 3 control or rebuild a supported control from raw click handlers without a concrete unmet requirement.
- Use Compose `Row`, `Column`, `Box`, and layout primitives for arrangement and custom content. They are not competing design systems.
- Reuse the existing application component when it has the same semantics. A wrapper must own a real repeated layout, policy, state, or lifecycle contract; do not add one merely to rename a call.
- Before copying a library control's layout/interaction implementation into the app, verify whether its public component and slots express the requirement. Prefer a thin semantic adapter over duplicated internal measurement/press logic; remove the redundant copy when the public path preserves the existing settings values and action semantics.
- Omit optional visual arguments when stock rendering fits. For a supported override, use the component's verified Defaults and color factories; preserve selected, disabled, pressed, and focus behavior.
- Use exported dimensions for component-owned details. Do not infer a `*Defaults` member or overload from another component.

## Theme and system appearance

- Provide one application-owned `MiuixTheme` around the routed UI subtree. Pages consume it rather than creating independent themes.
- Resolve system/light/dark and dynamic-color modes at the application boundary. Remember the theme controller with every changing input, including a selected key color.
- Keep theme preferences observable so changes update existing pages and bars. Register and unregister preference listeners with the same owner/lifetime.
- On Android, enable the intended edge-to-edge policy at the window boundary and derive status/navigation-bar icon appearance from the resolved app theme, including forced light/dark modes. Do not use system-dark state alone when the app overrides it.
- When setting a pre-Compose window background to avoid startup flashes, use the same resolved appearance policy. This platform bootstrap is separate from screen-level color rules.
- Theme key-color swatches belong in the theme configuration. Runtime blur support and user blur preference are resolved separately from color mode.

## Surface and Color Roles

- Use component Defaults, `MiuixTheme.colorScheme`, or centralized application semantic tokens for UI colors. DO NOT scatter `Color(0x...)` literals or page-local replacement palettes through screens.
- Select colors by role. Pair container and content colors correctly; do not reuse an `on*` role on an unrelated background or approximate disabled styling with arbitrary alpha.
- Use supported primary/selected/error roles instead of reconstructing every state color. New domain roles belong in a shared palette with the relevant light/dark and dynamic-color behavior.
- Preserve original colors for multicolor artwork with an untinted drawing contract, such as `Color.Unspecified` where supported. Brand images, color-picker values, and content pixels do not justify literal colors for surrounding controls.
- Error, selection, capability, and progress information must also be expressed by text, shape, icon, or semantics when the state matters; color alone is insufficient.
- Visible errors/loading/status that need announcement must expose suitable text/semantics. A tooltip is not the only channel for a required action or error.

For semantic-role lookup, use the [design-language guide](../design-language.md#surface-and-color-roles), then verify the selected Defaults through `miuix-components`.

## Typography Roles

- Let standard components own their text styling. Explicit text uses `MiuixTheme.textStyles` or an established shared application style.
- Reuse a semantic role before specifying local size/weight literals. A custom summary/title wrapper may deliberately combine a theme text size with a shared emphasis weight; keep that choice consistent in the wrapper.
- A title identifies the setting/action; a summary explains effect, current value, prerequisite, or context. Do not repeat the same sentence in both.
- Truncated content needs a deliberate access path when the full value matters: wrapping, expansion, a detail view, or copy. Do not make long identifiers irretrievable to preserve a screenshot.

For the type-scale lookup, use the [design-language guide](../design-language.md#typography-roles).

## Shapes and clipping

- Preserve component-owned shapes. Do not wrap a stock component in another background/clip merely to reproduce its surface.
- For custom continuous rounded surfaces, use the target-supported squircle family where it expresses the intended appearance. Keep ordinary rounded shapes for component-matched blur masks, image thumbnails, drag surfaces, or intentional circle/capsule geometry. `RoundedCornerShape` is not categorically forbidden.
- Match a blur mask, shadow shape, and clipping boundary to the visible component geometry; reuse its verified corner dimension rather than an unrelated radius.
- Distinguish painting from clipping: a background alone does not clip images or child feedback. Clip at the boundary that owns the image or interaction; do not add redundant offscreen clipping to a paint-only background.
- Verify squircle availability, modifier ordering, and fallback in the actual target through [Styling, icons, and effects](../../../miuix-components/references/styling-icons-and-effects.md#squircle). A selected shape does not establish a performance claim.

## Page metrics and card composition

### Grouped content cards

- A labeled semantic group uses `SmallTitle` followed by `Card`. Keep the label outside the Card; do not add a second arbitrary gap below it.
- Grouped Card external horizontal padding is `12.dp`; external bottom spacing is `12.dp`. Assign each once. Do not stack an equivalent `Arrangement.spacedBy` or spacer on top of the same gap.
- Keep component-owned internal padding, such as Preference row margins, separate from the external gutter. Do not substitute the external `12.dp` for `insideMargin`.
- A small fixed group can keep multiple related rows in one Card. Do not split it into unrelated cards just for spacing, or require every form/detail/custom page to become a settings-card list.
- Long, dynamic collections need suitable lazy item boundaries and stable identity. Separate cards per independent entity are valid. If one visual group is segmented for laziness, preserve its corners, feedback clipping, semantics, and item identity; do not require an application-specific grouping helper.
- Dividers are explicit semantic separators; a Card does not imply automatic child gaps or dividers. Prefer whitespace/grouping before adding borders. Nested Cards need distinct containment, not decoration alone.

### Scroll-page content padding

The normative standalone, shell-hosted, and refresh padding rules are in [page-shell.md](page-shell.md#insets-and-content-padding). This heading is a routing anchor, not a second metric definition.

## Navigation shell metrics

Navigation spacing and adaptive-shell requirements are defined in [page-shell.md](page-shell.md#navigation-and-adaptive-layout).

## Icons, images, and tags

- Use Miuix icons for ordinary navigation and actions; secondary-page back uses `MiuixIcons.Back` with a localized action description and the existing navigator's back operation.
- Use an `IconButton` for a standalone action. Preserve its component-owned target and feedback; do not shrink the hit area to the visible glyph.
- Application/provider rows use a consistent `40.dp` icon box and `8.dp` rounded thumbnail shape. Keep placeholders the same size so loading cannot shift the row. This metric does not apply to hero artwork or media previews.
- Decorative icons next to equivalent text have no redundant content description. Multicolor marks retain their own colors when appropriate.
- A compact tag uses a shared component: `10.dp` horizontal / `6.dp` vertical inner padding, an optional `16.dp` icon, and a `4.dp` icon-text gap. Reuse the supporting text role. Give press feedback only when the tag actually has an action.
- Decode/load icons outside composition and use the cache/identity rules in [State and performance](state-and-performance.md#lists-and-image-loading).

## Hierarchy and Grouping

Use the [group rules](#grouped-content-cards) and [settings rules](settings-and-overlays.md#settings-hierarchy-and-dependent-controls) for grouping and dependency decisions. Preserve state and behavior while changing appearance; visual cleanup must not silently change what a control does.

## Language and resources

- User-facing labels, summaries, dialog actions, errors, permission explanations, and meaningful content descriptions use the project's resource/localization layer. Product names and external content keep their intended identity.
- In Compose, resolve localized text before a remembered model, then include those resolved values in its keys. Do not freeze `context.getString()` inside an unkeyed `remember`.
- Locale changes update configuration, localized context, and layout direction together at the application boundary. Do not build separate locale wrappers per page.
- Keep preference keys, enum values, package identifiers, and persisted route identity independent of translated display strings. Locale-sensitive sorting must refresh when its locale changes.

## Accessibility and content resilience

- Preserve control roles, checked/selected/disabled semantics, focus behavior, and labels when wrapping public components. A visual disabled color does not disable an input or announce its state.
- Keep touch/pointer targets usable; do not copy a compact decorative size onto an action target. Test keyboard/focus behavior on the platforms the app supports.
- Check long translations, large text, RTL, empty values, and narrow/short windows. Scroll or wrap long content without pushing confirmation actions beyond reach.
- Keep the primary action visually primary through supported button roles. Dangerous actions require clear wording and the applicable confirmation flow; errors remain understandable without color alone.
- Inspect rendered and interactive states when affected. Source or compilation evidence alone does not prove contrast, focus, IME, adaptive layout, or target-device behavior.

## Error and Failure States

The normative loading, empty, refresh, unavailable, retry, and feedback rules are in [State and performance](state-and-performance.md#loading-errors-and-feedback). Input and popup validation rules are in [Settings and overlays](settings-and-overlays.md#inputs-and-validation).

## When Customization Is Justified

Customize only for a supplied visual target/brand rule, an established reusable convention, a requirement the stock component cannot express, a platform/accessibility constraint, or a demonstrated defect after correct defaults and ownership. Keep the smallest coherent customization and apply the [exception contract](#exceptions-and-conformance).

Use the [Code Delivery Contract](../../SKILL.md#code-delivery-contract) for delivery facts. Guides and examples do not override these rules or confer permission to rewrite unrelated code.
