# Content and Overlay Composition Guide

Use this guide to choose content containment and transient-surface placement. Mandatory content metrics live in [Design rules](rules/design-system.md#grouped-content-cards); settings, drafts, popup lifetime, inputs, and actions live in [Settings and overlay rules](rules/settings-and-overlays.md). This guide explains application shapes rather than restating those requirements.

## Body composition

| Content relationship | Useful shape | Question to settle |
|---|---|---|
| Small labeled preference group | `SmallTitle` then `Card` with related rows | Does this label describe one shared concept? |
| Many independent entities | Lazy items/cards keyed by entity | Which identity survives updates/filtering? |
| Long visual group | Lazy rows with coherent shared containment if needed | Does splitting composition preserve visual/interaction boundaries? |
| Small form/detail | `Column.verticalScroll` | Which state is draft versus persisted? |
| Repeated or large form/detail content | Lazy container | Which sections can compose independently? |
| Search and results | Existing content filter or expanded search surface | Who owns query, focus, and result scroll? |

For example, a small group can appear as:

```kotlin
item { SmallTitle(text = "Feature Group") }
item {
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(horizontal = 12.dp)
            .padding(bottom = 12.dp),
    ) {
        SwitchPreference(
            checked = featureEnabled,
            onCheckedChange = { featureEnabled = it },
            title = "Enable Feature",
            summary = "Turn on core service",
        )
        AnimatedVisibility(visible = featureEnabled) {
            Column {
                OverlayDropdownPreference(
                    items = listOf("Standard", "High", "Ultra"),
                    selectedIndex = selectedQuality,
                    onSelectedIndexChange = { selectedQuality = it },
                    title = "Quality Mode",
                )
                SliderPreference(
                    value = intensity,
                    onValueChange = { intensity = it },
                    title = "Intensity",
                    valueText = "${(intensity * 100).toInt()}%",
                )
            }
        }
    }
}
```

The dimensions illustrate the [group rules](rules/design-system.md#grouped-content-cards). Page padding belongs to the primary content boundary described by [Page shell rules](rules/page-shell.md#insets-and-content-padding).

## Surface placement

```text
page Scaffold
├── bars and actions
├── primary content
└── intended page popup host
    └── page-scoped Overlay

separate window, when selected for its lifetime/platform behavior
└── Window dialog, sheet, or menu
```

Use the [popup-family rules](rules/settings-and-overlays.md#popup-family-and-lifetime) to choose the family and dismissal behavior. Exact host flags, defaults, finished callbacks, and window/IME API details go to `miuix-components`.

## A small mutually exclusive popup flow

This application-state sketch illustrates the normative [single active-surface contract](rules/settings-and-overlays.md#popup-family-and-lifetime). It is not a complete dialog implementation or a new library API:

```kotlin
private enum class ActiveOverlay { EditName, DeleteConfirm }

var activeOverlay by remember { mutableStateOf<ActiveOverlay?>(null) }
var nameDraft by remember { mutableStateOf(savedName) }

OverlayDialog(
    show = activeOverlay == ActiveOverlay.EditName,
    onDismissRequest = { activeOverlay = null },
    onDismissFinished = { nameDraft = savedName },
) {
    // Edit the draft; the confirm action commits it.
}
```

The second surface derives visibility from `ActiveOverlay.DeleteConfirm`. Reopening and finished-cleanup timing follow the selected component's actual lifecycle and the [popup rules](rules/settings-and-overlays.md#popup-family-and-lifetime). A retained editor initializes each new open session from the current saved value. Trigger hold-down can remain active after visibility is requested false until exit finishes; the sketch does not prescribe an unconditional draft reset or clearing a newer session's state.

For a reordering sheet, map draft order, selected keys, header actions, and the bounded drag list using [sheet rules](rules/settings-and-overlays.md#sheets-and-reordering). For loading or a covered Snackbar, use [feedback rules](rules/state-and-performance.md#loading-errors-and-feedback).

Verify the selected component calls through `miuix-components`, then use the [settings/overlay validation boundary](rules/settings-and-overlays.md#validation).
