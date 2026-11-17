# Design Language Decision Guide

Use this guide to decide hierarchy, semantic roles, grouping, and presentation. Mandatory appearance and customization requirements live in [Design system rules](rules/design-system.md); page material/motion lives in [Page shell rules](rules/page-shell.md). This guide supplies reasoning and lookup routes rather than another rule set.

## Decision order

1. Map the page boundary when ownership is unresolved using [Page structure](page-structure.md).
2. Identify the applicable topic rules, changed scope, and intentional project conventions.
3. Choose the interaction/content role before choosing its component.
4. Identify the intended semantic visual role before looking up a token or supported override.
5. Select the narrow composition guide, then verify the chosen concrete API through `miuix-components`.

## Surface and Color Roles

| Visual question | Meaning to establish | Lookup |
|---|---|---|
| What is the page background? | Canvas versus contained content | Theme roles |
| What distinguishes a grouped surface? | Shared containment versus page canvas | Card/surface Defaults |
| Which action is primary? | Primary, secondary, selected, or destructive intent | Component state/color factory |
| Why is a summary less prominent? | Supporting explanation versus interactive affordance | Preference text roles |
| Is this line decoration or separation? | Actual boundary/group separation | Divider/outline role |
| How should disabled content look? | Disabled behavior/state, not simply lower opacity | Component disabled Defaults |
| Does a brand icon need tint? | Multicolor artwork versus semantic monochrome action | Content/artwork contract |

The [color rules](rules/design-system.md#surface-and-color-roles) own the allowed sources. Exact token mappings, factory names, and pinned Defaults are maintained in the companion [Color lookup](../../miuix-components/references/color-lookup.md), not duplicated here. A request such as “蓝色主按钮” first needs the distinction between primary intent and a fixed brand hue.

## Typography Roles

A page title, section label, control title, supporting summary, long-form body, and compact tag convey different roles. Establish that hierarchy from the content and neighboring components rather than choosing a numeric size first.

The [typography rules](rules/design-system.md#typography-roles) own style sourcing and customization. The actual theme text scale and component-owned styles belong to [Setup and theme](../../miuix-components/references/setup-and-theme.md) and the selected component's contract.

## Grouping and material

Decide whether content is a semantic group, an independent entity, or a form/detail body. Use [Content and overlays](content-and-overlays.md) for those shapes and the [group rules](rules/design-system.md#grouped-content-cards) for metrics. Visual grouping and lazy composition boundaries are separate decisions.

Ordinary blurred chrome follows the [standard bar recipe](rules/page-shell.md#standard-top-app-bar). Explicit Glass-family work uses [Glass compositions](glass-compositions.md). A translucent-looking request does not automatically mean a Glass component dependency.

## State presentation and customization

Identify whether the operation is initial loading, refreshing existing content, a local action, an unavailable prerequisite, or an editing session. Use the [state/feedback rules](rules/state-and-performance.md#loading-errors-and-feedback) and [input/popup rules](rules/settings-and-overlays.md) for the required behavior.

Use the [customization criteria](rules/design-system.md#when-customization-is-justified) when a concrete visual target or platform need exceeds defaults. Delivery evidence follows the entrypoint's [Code Delivery Contract](../SKILL.md#code-delivery-contract).
