# Greenfield Page

Use this reference only when the page has no usable existing host/route/inset contract, or the user explicitly authorizes replacing the shell. It defines the starting boundary for a new page and applies the design profile as the initial visual system; it does not prescribe a settings template or a fixed list of Miuix components.

## What is fixed

Fix responsibilities before choosing components:

- one application-owned MiuixTheme for the UI subtree;
- one route/back-stack owner;
- one owner for persistent navigation chrome;
- one page host only when the page owns a real bar, floating/bottom slot, snackbar, overlay host, or content-window contract;
- one primary vertical content/scroll owner per page boundary;
- one named shell-to-page inset handoff and one final content consumer;
- transient surface state owned by the host that renders it.

The content can use any semantically suitable Miuix component, Compose primitive, or application component. A settings page is still an ordinary page: SmallTitle, Card, Preference rows, a top bar, and a bottom bar are choices, not consequences of the page name.

## The profile is the starting system

When there is no existing design system to preserve, use this Skill's declared profile rules directly:

- a semantic grouped section uses `SmallTitle` followed by a `Card` with `12.dp` external horizontal inset and `12.dp` bottom gap;
- an ordinary standalone scroll page consumes the `Scaffold` padding once and adds `16.dp` bottom breathing room;
- component-owned internal padding, colors, shapes, and control dimensions come from verified Miuix Defaults rather than guessed replacements;
- shell navigation, Glass, PullToRefresh, and overlay values come from their matching composition reference and are not mixed into ordinary page metrics.

These are the Greenfield defaults. They can be changed only by a concrete product requirement or an explicitly chosen alternative design direction, not by arbitrary per-section tuning.

## Starting procedure

1. **State the result.** Rewrite the request as a visible page result and separate user requirements from Skill defaults.
2. **Map the boundary.** Name the theme, route owner, page entry, host, primary content owner, persistent state owner, and transient layers.
3. **Write the skeleton.** Use this shape as a starting point:

   Application root
   └── one MiuixTheme
       └── route/shell owner
           ├── optional persistent navigation
           └── active page boundary
               └── page host, only if it owns host behavior
                   ├── optional top slot
                   ├── one primary content owner
                   ├── optional bottom/floating/snackbar slots
                   └── optional page-scoped overlay

4. **Assign insets.** Decide which shell and page host emit PaddingValues, combine their intentional handoff, and apply the result once at the final content boundary. Do not add guessed system-bar constants as a second policy.
5. **Apply the profile.** Read the relevant [Design language and Defaults](design-language.md) and composition reference. Fix the declared metrics and combinations before adding local spacing or decoration.
6. **Choose semantics.** Select the primary content primitive and Miuix components by interaction role. Read Page composition routes only to select the one narrow composition guide that the skeleton actually needs.
7. **Choose state owners.** Keep selected destination, form data, loading, and business state in the screen/model owner. Keep popup, dialog, expanded, and transient host state near the rendering host.
8. **Verify APIs.** Send only the exact symbols that will be called to miuix-components. Treat composition snippets as placement guidance, not compile evidence.
9. **Validate the boundary.** Compile the affected target, then check insets, state transitions, accessibility, compact/wide behavior, and rendered states that the new page actually supports.

## Minimal delivery record

Use this record before implementation when the page is non-trivial:

    Result:
    Existing theme/host:
    Route/back-stack owner:
    Page boundary:
    Navigation-chrome owner:
    Primary content/scroll owner:
    Inset handoff and final consumer:
    Host slots and transient layer:
    Persistent state owner:
    Component choices by semantic role:
    Exact API symbols to verify:
    Validation still pending:

Unknown values stay unknown until inspected. Do not fill them with a familiar Example pattern.

## When to stop using this reference

- If a project already has a functioning page scaffold, route host, PageUtils-like helper, provider, state flow, or gesture chain, switch to [Brownfield evolution](brownfield-evolution.md).
- If the new page combines pull-to-refresh, overscroll, a collapsing bar, or a pager gesture, also read [Gesture dynamics](gesture-dynamics.md).
- If the task becomes a standalone API or dependency question, hand that narrow question to miuix-components.
