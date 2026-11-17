# Design Language and Defaults

Use this reference for the concrete visual rules defined by this Skill: profile metrics, typography, semantic colors, grouping, component Defaults, visual hierarchy, and failure-state presentation. It does not define page ownership; use [Page structure and ownership](page-structure.md) for that. This is the HyperLyric-derived design specification selected by this Skill, not an official rule that every Miuix page or application must follow. Exact token names, signatures, and component-owned Defaults contracts belong to the `miuix-components` Skill.

Read only the section matching the visual question; do not load color lookup, error-state, and typography guidance for an unrelated styling decision.

Contents: [decision order](#decision-order) · [surface and color](#surface-and-color-roles) · [typography](#typography-roles) · [component Defaults](#component-defaults) · [page metrics and card composition](#page-metrics-and-card-composition) · [hierarchy](#hierarchy-and-grouping) · [errors and loading](#error-and-failure-states) · [state and async](#state-and-async-ownership) · [customization](#when-customization-is-justified)

## Decision Order

Make each visual decision in this order:

1. Resolve the page/application skeleton and ownership with [Page structure and ownership](page-structure.md) when the task is page-level.
2. In Greenfield work, apply this profile's declared metrics and combinations. In Brownfield work, preserve an established target convention when it is intentional and coherent.
3. Use the public Miuix component that owns the behavior and its default parameters.
4. If customization is necessary, use that component's `*Defaults` object and `MiuixTheme` semantic tokens.
5. Use Compose layout primitives such as `Row`, `Column`, `Box`, padding, and size constraints to arrange Miuix components.
6. Add custom colors, shapes, drawing, or effects only for a concrete product requirement that the public component and defaults cannot express.

Do not introduce a Material or Material 3 component merely because its API is familiar. First verify that Miuix does not provide the intended component or interaction.

## Baseline versus choice

Keep these categories separate when explaining or reviewing a design:

| Category | Default in this profile | What can override it |
|---|---|---|
| Structural responsibility | Theme, route, host, scroll, inset, persistent state, and transient state each have a clear owner | Existing target architecture or a concrete product behavior |
| Component semantics | Use the public component that owns the interaction and its stock Defaults | A different public component or application-owned component better expresses the role |
| Visual hierarchy | Use semantic theme roles, Miuix text styles, meaningful grouping, and whitespace before custom decoration | A supplied visual target, brand rule, accessibility need, or proven visual defect |
| Profile metrics | Use the page-level values and combinations documented below by default | An established Brownfield convention, a responsive constraint, or a concrete product requirement |

Do not call a profile default a Miuix API requirement. Do not call a target-project convention a universal design rule.

Glass is an optional visual family, not a stronger version of the ordinary profile. If the product chooses Glass, the recorded `Backdrop`, opaque fallback, surface exclusion, and popup-host/`BoxScope` boundary become structural requirements for that family; material presets, strokes, blur strength, and translucency remain visual choices. Do not mix a translucent-looking custom surface into an ordinary page without adopting the corresponding backdrop and fallback contract.

## Surface and Color Roles

Prefer the component's default colors. When code must choose a token directly, select it by semantic role rather than by its current light-theme appearance.

For a natural-language request such as “蓝色主按钮” or “灰色摘要文字”, use the Color Semantics and Visual Lookup reference in the `miuix-components` Skill. It verifies the pinned `Colors` source and component Defaults, and distinguishes a semantic role from a fixed default-theme hue.

| UI role | Start with | Notes |
|---|---|---|
| Page canvas | `background` + `onBackground` | Use for the root page and content placed directly on it |
| General elevated or chrome surface | `surface` + `onSurface` | Follow the closest Example or component default before applying it manually |
| Grouped content container | `CardDefaults.defaultColors()` | At the pinned snapshot, Card already uses `surfaceContainer` + `onSurfaceContainer` |
| Primary action or selected control | The component's primary/selected Defaults | For example, use `ButtonDefaults.buttonColorsPrimary()` instead of recreating primary and disabled colors |
| Preference summary | `BasicComponentDefaults.summaryColor()` | Its default uses `onSurfaceVariantSummary` and handles disabled state |
| Row action or trailing affordance | Component default or `onSurfaceVariantActions` | Use the latter only where an action-role color is required explicitly |
| Divider or boundary | `dividerLine` or `outline` | Use `Divider` defaults for dividers; use `outline` for a true outline role |
| Dialog or popup scrim | Component default | The window and overlay implementations use the semantic `windowDimming` role |
| Disabled content | The component's disabled state/defaults | Do not simulate disabled UI with arbitrary alpha; Miuix has role-specific disabled tokens |

Do not pair an `on*` color with an unrelated container. Do not copy literal colors from the Example: theme modes and dynamic color can change their rendered values.

## Typography Roles

Let Miuix components choose typography unless the product requires custom text. For explicit `Text`, choose from `MiuixTheme.textStyles` after comparing the target project's existing hierarchy and the closest Example.

| Content | Preferred starting point |
|---|---|
| Page or prominent heading | The appropriate `title1`–`title4` scale used by the nearest page pattern |
| Section label | `SmallTitle`; its default style is `subtitle` |
| Preference or component title | Keep `BasicComponent`/Preference defaults; the title uses the component's headline role |
| Preference summary | Keep the component default; it uses the supporting body role |
| Long-form or ordinary text | Select among `main`, `paragraph`, `body1`, and `body2` by context |
| Compact supporting text | `footnote1` or `footnote2` when the nearby design uses a footnote role |
| Button label | Keep the Button default or use `button` when building a compatible custom label |

These names are a type scale, not a rigid HTML-like heading hierarchy. Do not choose a style from its numeric size alone, and do not hardcode font size or weight when a semantic style already fits.

## Component Defaults

Search the selected component's verified `*Defaults` contract before adding a magic number. Use the corresponding color lookup in the `miuix-components` Skill for exact token mappings and component color factories.

- Omit optional visual parameters when the stock result is acceptable.
- Call the component's color factory when changing one supported role, instead of recreating all state colors manually.
- Reuse exported dimensions and margins when they express the same component contract.
- Treat spacing found in Miuix `example/shared/` as supporting composition evidence. The profile metrics below are the design rules to apply; neither source is a Miuix library constant.
- Recheck source when a Defaults member is not shown in the component doc; do not infer its name from another component.

## Page metrics and card composition

These are the default page metrics of this design specification. They are not optional suggestions for a new page, and they are not `Card`, `Scaffold`, or Preference library defaults. In Brownfield work, keep an established project convention only when the surrounding pages use it intentionally and consistently.

### Grouped content cards

For a section with a label and shared containment, use this composition by default:

```kotlin
SmallTitle(text = sectionTitle)
Card(
    modifier = Modifier
        .fillMaxWidth()
        .padding(horizontal = 12.dp)
        .padding(bottom = 12.dp)
) {
    // Related Preference rows or other content
}
```

Rules:

- The Card's external horizontal inset is `12.dp`.
- The Card's external bottom gap is `12.dp`; this is the distance to the next section, not Card's internal padding.
- Put `SmallTitle` directly before the Card. Do not add a second ad-hoc spacer between the title and its Card.
- Keep related rows inside one Card. Do not create one Card per row only to obtain spacing.
- Let Miuix components own their internal padding through their verified Defaults. Do not turn the external `12.dp` into a replacement for `insideMargin`.
- Use another composition when the content is not a semantic group; this rule does not require every page to become a list of Cards.

This pattern is repeated by the reference consumer's settings and section pages. The rule is the composition and its metric, not a requirement that the target project contain the same file names.

### Scroll-page content padding

For a standalone page-owned `Scaffold` with an ordinary scroll container, consume the host padding once and add the profile's `16.dp` bottom breathing room:

```kotlin
val contentPadding = PaddingValues(
    top = scaffoldPadding.calculateTopPadding(),
    start = 0.dp,
    end = 0.dp,
    bottom = scaffoldPadding.calculateBottomPadding() + 16.dp,
)
```

- Apply this to the primary `LazyColumn` or equivalent scroll owner.
- Do not repeat the same top or bottom inset on every Card or child.
- When the page is inside a persistent shell with a bottom navigation bar, merge the shell-supplied `PaddingValues` through the page boundary once. Do not add the standalone-page `16.dp` a second time when the shell already supplies the required bottom space.
- When using the Miuix PullToRefresh integration path, follow its verified content-padding and gesture contract. Do not blindly add the ordinary-page `16.dp` on top of the refresh wrapper's padding.

The profile's stable page metrics are therefore `12.dp` for grouped Card horizontal/bottom spacing and `16.dp` for ordinary standalone scroll-page bottom breathing room. Values such as Glass navigation margins, dialog field gaps, or preview-specific spacing belong to their narrower composition references rather than this general page rule.

## Hierarchy and Grouping

- Use [Page structure and ownership](page-structure.md) to give every page one clear structural shell. The shell is a responsibility boundary, not a requirement to use `Card`, `SmallTitle`, or Preference components on every page. Avoid nested Scaffolds or nested Cards unless they represent distinct behavior or containment.
- For grouped settings content, use `SmallTitle` + `Card` as the default section composition when those components express the section label and containment. Keep unrelated actions in separate groups; use another composition when the content does not have those semantics.
- In the pinned source, Card is a plain `Column` with `CardDefaults.InsideMargin = 0.dp`; it does not insert gaps or Dividers between children. Preference rows provide their own internal padding and disabled styling. Do not add row spacing or separators by habit—add a Divider only when the grouping remains ambiguous.
- Place `SmallTitle` before its Card for a section label. Its own Defaults provide label padding; it is not a row inside the Card and there is no public `SectionHeader` component at this tag.
- For grouped content, use the page metrics above: `12.dp` outer horizontal inset and `12.dp` Card bottom gap. These are rules of this design specification, not claims about Miuix component internals.
- Avoid nested Cards for visual decoration. Nest only when the inner Card has a distinct containment or interaction role that remains understandable in accessibility traversal.
- Use the title for the setting or action and the summary for consequence, status, or context. Do not repeat the same wording in both.
- Keep the primary action visually dominant and secondary/destructive actions distinguishable through supported component roles, placement, and wording.
- Prefer whitespace and grouping over extra borders. Add a Divider only when separation is still ambiguous.
- Preserve state ownership and interaction semantics while changing appearance; visual cleanup must not silently change behavior.

## Error and Failure States

- Do not invent Material-style error parameters for a Miuix field. Track validation state outside the field, use its verified error-color contract with the semantic error role, and place concise supporting text below it. Do not communicate the error by color alone.
- Preference components do not expose one uniform error flag. For a row-level validation problem, keep the row visible, explain the problem in its summary or nearby inline text, and use an exposed error-role color only after the `miuix-components` Skill verifies that row's signature.
- Use `Snackbar` for transient operation/network feedback when the current content remains usable. Use an inline page/section error with a retry action for persistent or blocking failures; preserve entered form data.
- Validate error, disabled, loading, empty, and retry states independently in light/dark and dynamic color modes when affected.

### Loading and async states

- For initial page loading, use `ProgressIndicator` or a meaningful placeholder and keep the surrounding hierarchy stable.
- For list refresh, use `PullToRefresh` with a hoisted `isRefreshing` state and an explicit refresh callback; preserve existing content while the refresh runs.
- For a single action, show local progress and prevent duplicate submission when appropriate; use `Snackbar` for the eventual success/failure result rather than as the only loading indicator.

### State and async ownership

- Keep persistent or business state in the screen owner/ViewModel; use `remember` for transient presentation state such as a popup or pressed state.
- Keep a form draft in `rememberSaveable` where the target supports it, or in the screen owner when restoration/serialization requires more control.
- Model loading, empty, error, success, disabled, and retry states explicitly rather than deriving them from one loosely related Boolean.
- Emit one-shot navigation or Snackbar effects from an explicit event/effect path, launched once per event rather than directly during composition.
- Cancel obsolete work, prevent duplicate submission where appropriate, and preserve user input when an async operation fails.

## When Customization Is Justified

Customize beyond defaults only when at least one of these is true:

- The user supplied a concrete visual target, brand rule, or screenshot that requires it.
- The target project already has an intentional reusable token or component convention.
- The public Miuix component cannot represent the required hierarchy, state, or platform constraint.
- A visual defect remains after the correct component, theme, host, and defaults are in place.

Keep the smallest coherent customization, document any non-obvious reason in code, and verify light/dark or dynamic color behavior when affected.

Use the [Code Delivery Contract](../SKILL.md#code-delivery-contract) for delivery facts. Do not load the UI review workflow unless the task is specifically an existing-screen review.
