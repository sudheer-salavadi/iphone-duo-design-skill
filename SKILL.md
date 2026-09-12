---
name: iphone-duo-design
description: Grounds iPhone Duo (Apple's foldable iPhone, dual displays + hinge) product design and UX work in Apple's Human Interface Guidelines, physical device specs, Xcode/API capabilities, and App Store Connect submission requirements — device poses, reserved camera/folding regions, split views, arrangement views, vertical toolbar/tab-bar placement, exact screen resolutions, new hinge/reserved-region/arrangement-view APIs, screenshot dimensions, and exactly what differs from standard iPhone design vs. what carries over unchanged (accessibility, notifications, widgets, Live Activities, privacy, commerce). Use whenever designing or critiquing a feature, screen, or flow for iPhone Duo specifically, scoping engineering/asset work for it, or deciding whether a design needs Duo-specific treatment at all.
---

# iPhone Duo Design Feasibility (Apple HIG + Developer Docs + App Store)

Use this skill to ground **iPhone Duo** (Apple's foldable iPhone — dual displays, front cameras on each, central hinge, announced September 9, 2026, shipping ~October 23, 2026) product-design and UX work in real platform capabilities and constraints — spanning the Human Interface Guidelines, physical device specs, Xcode/API-level engineering feasibility, and App Store Connect submission requirements — and to know precisely where Duo design differs from standard iPhone design versus where standard iOS guidance simply carries over.

## Start here

1. **[references/iphone-duo.md](references/iphone-duo.md)** — the primary HIG reference. Device anatomy (outer/inner display + cameras), device poses, the four reserved regions, dynamic layouts, split views on Duo, arrangement views (split/overlay), vertical toolbar/tab-bar placement, and games — each split into Capabilities / Limitations / Design implications, cited to the source HIG page.
2. **[references/standard-iphone-vs-duo.md](references/standard-iphone-vs-duo.md)** — the scoping guide. A carries-over-unchanged list, a side-by-side differences table, and scoping questions to ask before designing or reviewing any feature ("does this toolbar assumption break on Duo?", "is this a natural split-view candidate?", "does this only exist on one display?"). **Load this file whenever the task is to decide how much Duo-specific work a feature actually needs**, not just when designing something Duo already knows it needs.
3. **[references/device-specs.md](references/device-specs.md)** — physical specs: screen sizes/resolutions/ppi for both displays, physical dimensions/weight, chip/storage/battery/durability. Load when a design decision depends on exact pixel/point math or device-weight/reachability assumptions.
4. **[references/xcode-development.md](references/xcode-development.md)** — engineering feasibility: Xcode/Simulator support status, the new hinge/reserved-region/arrangement-view APIs (SwiftUI + UIKit), SDK-version-dependent rendering behavior, migration requirements for existing apps. **Load this before treating any Duo design concept as buildable** — several capabilities described here are still pre-release/beta.
5. **[references/app-store-submission.md](references/app-store-submission.md)** — App Store Connect screenshot requirements (exact pixel dimensions, and that asset upload isn't live yet), and confirmation that the App Review Guidelines currently have zero Duo-specific language. Load when scoping a launch/submission plan, not just a design.

## How to use this

- **Critiquing a Duo design**: flag anything that violates a stated hard constraint (fixed-width layout, horizontal toolbar unmodified from standard iPhone, content ignoring a reserved region, a custom two-pane layout that should be a system split view) as a concrete feasibility problem — cite the specific rule and source.
- **Generating/ideating for Duo**: lead with the platform capabilities that make Duo distinct (split view that expands/collapses automatically between displays, vertical toolbars with visibility-priority overflow, arrangement views for pose-driven pane management) rather than treating Duo as "iPhone but bigger."
- **Deciding scope**: before assuming a feature needs bespoke Duo design work, run it through the scoping questions in standard-iphone-vs-duo.md — many features are Duo-agnostic (accessibility, notifications, widgets, commerce rules) and don't need re-design, only validation.
- **Prefer a feasible alternative over a flat "no."** When a concept is infeasible as specified (a fixed-pixel-width mockup, a per-pose bespoke layout, navigation nested inside an arrangement view), say what's actually possible instead (size-class-driven layout, one adaptive layout via system containers, navigation wrapping the arrangement view) rather than only stopping the idea.
- **Numbers and rules are load-bearing.** Where a reference gives a specific constraint (four reserved regions, vertical-axis item ordering, "don't override default bar placement"), treat it as a hard check, not a rough guideline.

## Supporting reference files (standard iOS baseline — still applies to Duo unless noted otherwise)

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

The Designing for iPhone Duo HIG page is brand new (added September 9, 2026) and, as of this research, is the **only HIG page** with Duo-specific content — a direct check of Designing for iOS, Layout, Multitasking, and Live Activities found no Duo-specific updates yet, and a dedicated `arrangement-views` page does not exist (its content lives only inside the Duo page itself). Apple will likely expand Duo coverage into other HIG pages over time — if a question needs Duo-specific detail not present in references/iphone-duo.md, say it isn't confirmed yet rather than inventing it, and suggest checking developer.apple.com/design/human-interface-guidelines/designing-for-iphone-duo directly.

**Beyond the HIG, treat the following as explicitly unstable/unconfirmed** (each is flagged in detail in its file, don't smooth over the caveat when answering a question that touches it):
- Xcode 27.1 / iOS 27.1 SDK (the version that adds Duo support) is a **beta, not yet GA**, per Apple's own developer hub — no release notes page exists yet, and `UIHingeInteraction`, `UIViewReservedRegion`, and `UIArrangementViewController` have no confirmed standalone API reference pages (known only from Tech Talk transcripts). See [references/xcode-development.md](references/xcode-development.md).
- Exact **inner-display native pixel resolution** is not published on any developer.apple.com page found — the apple.com marketing specs page states 1878×2670px, which is used in [references/device-specs.md](references/device-specs.md), but this hasn't been cross-confirmed against an official developer points/pixels table (none exists yet for Duo).
- **App Store Connect cannot yet accept iPhone Duo screenshot/app-preview uploads** even though the pixel specification is published ("later this year" per Apple's Sept 9, 2026 release note) — see [references/app-store-submission.md](references/app-store-submission.md).
- The **App Review Guidelines contain zero mentions** of Duo/foldable/hinge/dual-display as of this research — don't assert a specific Duo design choice "will pass/fail review" as if a citable rule exists yet.

Separately, Apple restructured the rest of the HIG site in 2025: **Haptics, Camera, Lock Screen, Always-On Display, StandBy, Home Screen Quick Actions** no longer have independently retrievable dedicated pages (some content survives folded into the Widgets page — see references/notifications-widgets.md).

Reference content here is summarized/paraphrased from Apple's public HIG (developer.apple.com/design/human-interface-guidelines) as of September 2026, for internal design-feasibility use — always treat Apple's live HIG pages as the authoritative source of truth if a decision is high-stakes or the guidance may have since changed.
