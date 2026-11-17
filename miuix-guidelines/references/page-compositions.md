# Page Composition Routes

Use this file only after the page skeleton and ownership boundary are known. It is an index, not an implementation recipe. Select one narrow composition guide below; do not load every route.

Every composition guide keeps the same boundary: the page host owns its real bars and transient layers, the primary content owns the intended scroll, and the final content boundary consumes the host padding once.

Exact Miuix signatures, imports, defaults, and version-sensitive behavior belong to miuix-components. These guides describe placement and ownership; verify every symbol before copying code.

## Select one composition guide

| User-visible need | Read |
|---|---|
| Pinned title over scrolling content, or a large title that collapses | [Top bar compositions](top-bar-compositions.md) |
| Compact, floating, or adaptive bottom navigation; floating action group | [Navigation and action compositions](navigation-compositions.md) |
| Explicit Glass page shell, Glass bottom bar, or Glass backdrop with a pinned title | [Glass compositions](glass-compositions.md) |
| Grouped content, forms, details, dialogs, sheets, menus, or page-scoped overlays | [Content and overlay compositions](content-and-overlays.md) |

If a request combines multiple rows, read the row that owns the interaction being changed first, then add only the other guide needed for the separate slot. For pull-to-refresh, overscroll, pager gestures, or rebound-driven visual effects, read [Gesture dynamics](gesture-dynamics.md) instead of copying a basic collapsing-bar snippet.

## Shared ownership checks

- Reuse the existing theme and route owner.
- Use one host for each real shell/page boundary; do not add a Scaffold only for background or convenient padding.
- Keep one primary content/scroll owner unless the nested relationship is intentional and source-verified.
- Apply the intentional shell/page padding handoff once at the final content boundary.
- Keep persistent navigation, form, and business state outside visual component branches.
- Treat every recipe as a starting shape. Product requirements and an existing project contract may justify another composition.

## Stop rule

Stop reading this index once the narrow guide has named the host, primary content owner, inset consumer, state owner, and validation boundary. If the remaining question is an exact Miuix symbol, hand only that symbol to miuix-components.
