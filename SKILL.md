---
name: ios-hig-feasibility
description: Grounds iPhone/iOS product design and UX work in Apple Human Interface Guidelines technical capabilities and constraints — hard numeric limits (tap targets, type sizes, contrast ratios, Live Activity/widget dimensions, character counts, timing windows), platform capabilities (Live Activities, Dynamic Island, widgets, App Clips, notifications, App Intents/Siri, SharePlay, Sign in with Apple, In-App Purchase), and App-Store-review-risk patterns. Use whenever designing or critiquing an iPhone feature, screen, flow, notification, widget, Live Activity, permission/onboarding flow, or any iOS UI concept, to check what's actually technically feasible and shippable before committing to a design direction.
---

# iOS/iPhone Design Feasibility (Apple HIG)

Use this skill to keep iPhone product-design and UX work anchored in what iOS actually supports — not just visual style, but hard technical constraints and platform capabilities from Apple's Human Interface Guidelines (HIG). The goal: catch infeasible design concepts *before* they're built, and know when a platform capability makes something possible that a designer might not realize is available.

## How to use this

1. **Identify the topic(s)** the design touches (layout, a widget, a Live Activity, a permission prompt, a custom gesture, etc.).
2. **Load the relevant reference file(s) below** — each is dense, factual, and organized as Capabilities / Limitations / Design implications, with the source HIG URL cited per topic so claims can be checked against the current page.
3. **Apply it two ways:**
   - **Critiquing a design**: flag anything that violates a stated hard limit (tap target size, character count, layout dimension, timing window, App-Store-review rule) as a concrete feasibility problem, not a style opinion — cite the specific constraint and its source.
   - **Generating/ideating a design**: surface platform capabilities the person may not know about (e.g., Live Activities reaching Lock Screen + Dynamic Island + Apple Watch Smart Stack + CarPlay from one implementation; SF Symbols' built-in animation presets; the Location Button for one-time access) so the design takes advantage of what iOS actually offers instead of reinventing it.
4. **Prefer a feasible alternative over a flat "no."** When a concept is infeasible (e.g., a live-updating widget, a tilt-to-navigate UI, a gesture-only action), say what's actually possible instead (periodic refresh + last-updated timestamp; motion input scoped to gameplay only; a button/menu fallback) rather than only stopping the idea.
5. **Numbers are load-bearing.** Where a reference gives a specific number (44×44pt tap target, 200% Dynamic Type, 4.5:1 contrast, 8-hour Live Activity ceiling, 4 notification actions), treat it as a hard check, not a rough guideline.

## Reference files

| File | Covers |
|---|---|
| [references/layout-navigation.md](references/layout-navigation.md) | Size classes & safe areas, tab bars/sidebars, in-app + Spotlight search, modals/sheets/popovers |
| [references/gestures-input.md](references/gestures-input.md) | Multi-touch gesture vocabulary and system-reserved zones, virtual/custom keyboards, buttons, text fields |
| [references/multitasking-lifecycle.md](references/multitasking-lifecycle.md) | Backgrounding/state restoration, App Clips, launch screens |
| [references/live-activities-dynamic-island.md](references/live-activities-dynamic-island.md) | Live Activities, Dynamic Island states, exact layout dimensions, StandBy |
| [references/notifications-widgets.md](references/notifications-widgets.md) | Notification actions/badges/rich UI, widget families/sizes, refresh-rate limits, Lock Screen/StandBy rendering |
| [references/accessibility.md](references/accessibility.md) | Dynamic Type %, minimum font/contrast/tap-target numbers per platform, Reduce Motion |
| [references/visual-system.md](references/visual-system.md) | Materials ("Liquid Glass"), Dark Mode, SF Symbols, App Icon composition rules |
| [references/sensors-hardware.md](references/sensors-hardware.md) | Motion (Core Motion) constraints, Maps (custom annotations, watchOS limits, attribution requirements) |
| [references/privacy-permissions.md](references/privacy-permissions.md) | Permission-prompt/pre-alert rules, App Tracking Transparency, Sign in with Apple button constraints |
| [references/commerce-collaboration-assistant.md](references/commerce-collaboration-assistant.md) | In-App Purchase UI rules, SharePlay, Siri/App Intents |

## Coverage gaps — do not fabricate

Apple restructured the HIG site in 2025 and several previously-standalone pages now 404 on developer.apple.com: **Haptics, Camera, Lock Screen, Always-On Display, StandBy, Home Screen Quick Actions** no longer have independently retrievable dedicated pages (some content survives folded into the Widgets page — see references/notifications-widgets.md). If a question needs specifics for one of these topics beyond what's in the reference files, say the current HIG page for it could not be confirmed rather than inventing numbers, and suggest checking developer.apple.com/design/human-interface-guidelines directly.

Reference content here is summarized/paraphrased from Apple's public HIG (developer.apple.com/design/human-interface-guidelines) as of September 2026, for internal design-feasibility use — always treat Apple's live HIG pages as the authoritative source of truth if a decision is high-stakes or the guidance may have since changed.
