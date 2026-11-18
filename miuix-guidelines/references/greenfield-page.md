# Greenfield Page Workflow

Use when there is no usable page/host contract or shell replacement is explicitly part of the task. An existing shell with a new feature is Mixed; use [Brownfield evolution](brownfield-evolution.md) for the shell. This file describes the work sequence, not another set of UI requirements.

## Starting procedure

1. **Interpret the request.** Separate the desired visible result from assumptions. Inspect available theme, route, platform, source set, and resolved Miuix version before declaring the boundary new.
2. **Map the page.** Identify route/navigation owner, page host, primary content, state owner, inset handoff, transient layers, and any shared page wrapper. Use the [ownership worksheet](page-structure.md#ownership-worksheet) only if these decisions are unresolved.
3. **Load applicable rules.** Start with [design-system scope and topic routing](rules/design-system.md#required-topic-rules). A normal scrollable page follows [standard TopBar](rules/page-shell.md#standard-top-app-bar); settings, popups, and async data add their matching rule sections.
4. **Choose one composition.** Use [top bar](top-bar-compositions.md), [navigation](navigation-compositions.md), [content/overlays](content-and-overlays.md), or explicit [Glass](glass-compositions.md) for the unresolved relationship. Content semantics decide the body within the selected standard shell.
5. **Verify APIs.** Hand the concrete symbols, host requirements, and target-version questions to `miuix-components`; treat guide code as placement examples.
6. **Implement and validate.** Apply the mapped boundary and rules, compile the affected target when available, then inspect the relevant visual/interaction states. Use the entrypoint's [Code Delivery Contract](../SKILL.md#code-delivery-contract).

Use the existing ownership worksheet only for unresolved structural decisions; do not require a second blueprint for a routine page. Unknown context stays unknown until inspected.

## Routing out

- Existing wrappers, routing, or state require [Brownfield evolution](brownfield-evolution.md) for those boundaries.
- Combined scroll/refresh/pager behavior uses [Gesture dynamics](gesture-dynamics.md).
- A standalone API/dependency question goes directly to `miuix-components`.
