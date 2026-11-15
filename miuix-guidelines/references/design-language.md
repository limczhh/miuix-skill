# Design Language and Defaults

Use this reference only for the visual baseline defined by this Skill: typography, semantic colors, grouping, component Defaults, visual hierarchy, and failure-state presentation. It does not define page ownership or require the Example workflow. This is one practical Miuix usage profile, not an official rule that every Miuix page or application must follow. The hierarchy guidance is a practical decision rule derived from Miuix source defaults and the first-party Example; exact token names, signatures, and Defaults contracts belong to the `miuix-components` Skill.

Read only the section matching the visual question; do not load color lookup, error-state, and typography guidance for an unrelated styling decision.

Contents: [decision order](#decision-order) · [surface and color](#surface-and-color-roles) · [typography](#typography-roles) · [component Defaults](#component-defaults) · [page composition defaults](#page-composition-defaults) · [hierarchy](#hierarchy-and-grouping) · [errors and loading](#error-and-failure-states) · [state and async](#state-and-async-ownership) · [customization](#when-customization-is-justified)

## Decision Order

Make each visual decision in this order:

1. Resolve the page/application skeleton and ownership with [Page structure and ownership](page-structure.md) when the task is page-level.
2. Preserve an intentional convention already used by the target project.
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
| Spacing fallback | Use the page-level values documented below only when the target has no spacing convention | Existing project tokens, host/component padding, or measured layout requirements |

Do not call a profile default a Miuix API requirement. Do not call a target-project convention a universal design rule.

## Surface and Color Roles

Prefer the component's default colors. When code must choose a token directly, select it by semantic role rather than by its current light-theme appearance.

For a natural-language request such as “蓝色主按钮” or “灰色摘要文字”, use the Color Semantics and Visual Lookup reference in the `miuix-components` Skill. It verifies the stable `Colors` source and component Defaults, and distinguishes a semantic role from a fixed default-theme hue.

| UI role | Start with | Notes |
|---|---|---|
| Page canvas | `background` + `onBackground` | Use for the root page and content placed directly on it |
| General elevated or chrome surface | `surface` + `onSurface` | Follow the closest Example or component default before applying it manually |
| Grouped content container | `CardDefaults.defaultColors()` | At the stable snapshot, Card already uses `surfaceContainer` + `onSurfaceContainer` |
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
- Treat spacing found only in `example/shared/` as composition evidence, not as a library constant.
- Recheck source when a Defaults member is not shown in the component doc; do not infer its name from another component.

## Page composition fallback values

These are Skill-level fallback values for a grouped page when the target project has no established spacing system. They are not `Card`, `Scaffold`, or Preference library defaults:

- Use `12.dp` as the default horizontal outer inset for a grouped content container.
- Use `12.dp` as the default gap after a grouped container before the next section.
- When a page uses a `Scaffold` and has no existing bottom-spacing convention, add `16.dp` of bottom breathing room after the host's supplied content padding.

Apply these values at the page-composition boundary, not again inside every component. Do not add them when the existing host, component `insideMargin`, or project spacing system already supplies the same space. Keep the values consistent within one page rather than tuning each section independently.

## Hierarchy and Grouping

- Use [Page structure and ownership](page-structure.md) to give every page one clear structural shell. The shell is a responsibility boundary, not a requirement to use `Card`, `SmallTitle`, or Preference components on every page. Avoid nested Scaffolds or nested Cards unless they represent distinct behavior or containment.
- For grouped settings content, prefer `SmallTitle` + `Card` when those components express the section label and containment; this is a content pattern, not a requirement for every page. Keep unrelated actions in separate groups.
- In the stable source, Card is a plain `Column` with `CardDefaults.InsideMargin = 0.dp`; it does not insert gaps or Dividers between children. Preference rows provide their own internal padding and disabled styling. Do not add row spacing or separators by habit—add a Divider only when the grouping remains ambiguous.
- Place `SmallTitle` before its Card for a section label. Its own Defaults provide label padding; it is not a row inside the Card and there is no public `SectionHeader` component at this tag.
- For grouped content without a target-project spacing convention, use the page composition defaults above: `12.dp` outer inset and `12.dp` section gap. These are composition values, not a claim about Miuix component internals.
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
