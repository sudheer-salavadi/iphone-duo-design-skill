---
name: iphone-duo-design
description: Grounds iPhone Duo (Apple's foldable iPhone) product design, UX, and engineering-feasibility decisions in the HIG, device specs, Xcode/API capabilities (hinge, reserved regions, arrangement views, vertical toolbars), and App Store Connect requirements. Use when designing, critiquing, or scoping any feature, screen, or flow for iPhone Duo, or deciding whether it needs Duo-specific treatment at all.
---

# iPhone Duo Design Feasibility (Apple HIG + Developer Docs + App Store)

Use this skill to ground **iPhone Duo** (Apple's foldable iPhone — dual displays, front cameras on each, central hinge, announced September 9, 2026, shipping ~October 23, 2026) product-design and UX work in real platform capabilities and constraints — spanning the Human Interface Guidelines, physical device specs, Xcode/API-level engineering feasibility, and App Store Connect submission requirements — and to know precisely where Duo design differs from standard iPhone design versus where standard iOS guidance simply carries over.

## Start here

1. **[references/iphone-duo.md](references/iphone-duo.md)** — the primary HIG reference. Device anatomy (outer/inner display + cameras), device poses (including game-specific guidance), reserved regions, the **displacement** design pattern, dynamic layouts, split views, arrangement views, vertical toolbar/tab-bar placement, sheets, and system multitasking/Picture-in-Picture — each split into Capabilities / Limitations / Design implications, cited to the source HIG page and Apple's own Sept 2026 Tech Talks.
2. **[references/standard-iphone-vs-duo.md](references/standard-iphone-vs-duo.md)** — the scoping guide. A carries-over-unchanged list, a side-by-side differences table, and scoping questions to ask before designing or reviewing any feature ("does this toolbar assumption break on Duo?", "is this a natural split-view candidate?", "does this only exist on one display?"). **Load this file whenever the task is to decide how much Duo-specific work a feature actually needs**, not just when designing something Duo already knows it needs.
3. **[references/device-specs.md](references/device-specs.md)** — physical specs: screen sizes/resolutions/ppi for both displays, physical dimensions/weight, chip/storage/battery/durability. Load when a design decision depends on exact pixel/point math or device-weight/reachability assumptions.
4. **[references/xcode-development.md](references/xcode-development.md)** — core engineering feasibility: Xcode/Simulator support status, the exact size-class matrix, screen/display access, safe areas, `UIRequiresFullScreen`, split-view multitasking and multi-window rules, scene accessories, the hinge/reserved-region/arrangement-view APIs (with real code from Apple's own talks). **Load this before treating any Duo design concept as buildable** — several capabilities described here are still pre-release/beta.
5. **[references/vertical-toolbars.md](references/vertical-toolbars.md)** — the full vertical toolbar/tab-bar/sheet API surface (opting in, item ordering, axis-behavior overrides, badges, overflow/compression configuration, disabling it) with exact code. Load whenever a task involves toolbar/tab-bar/navigation-bar specifics on Duo — this is one of the highest-density, most failure-prone areas.
6. **[references/camera.md](references/camera.md)** — the dual front-camera API surface: virtual vs. individual camera capability tradeoffs (resolution/fps/depth), the Direction Coordinator (cameras aren't fixed-facing on Duo), preview/rotation polish. Load for any camera-related feature.
7. **[references/app-store-submission.md](references/app-store-submission.md)** — App Store Connect screenshot requirements (exact pixel dimensions, and that asset upload isn't live yet), and confirmation that the App Review Guidelines currently have zero Duo-specific language. Load when scoping a launch/submission plan, not just a design.

## How to use this

- **Critiquing a Duo design**: flag anything that violates a stated hard constraint (fixed-width layout, horizontal toolbar unmodified from standard iPhone, content ignoring a reserved region, a custom two-pane layout that should be a system split view, a custom `UIToolbar` that won't get vertical-bar behavior at all) as a concrete feasibility problem — cite the specific rule and source.
- **Generating/ideating for Duo**: lead with the platform capabilities that make Duo distinct (split view that expands/collapses automatically between displays, vertical toolbars with visibility-priority overflow, arrangement views for pose-driven pane management, the displacement pattern for keeping controls reachable across folds, the Camera Capture Accessory for dual-display camera experiences) rather than treating Duo as "iPhone but bigger."
- **Deciding scope**: before assuming a feature needs bespoke Duo design work, run it through the scoping questions in standard-iphone-vs-duo.md — many features are Duo-agnostic (accessibility, notifications, widgets, commerce rules) and don't need re-design, only validation.
- **Prefer a feasible alternative over a flat "no."** When a concept is infeasible as specified (a fixed-pixel-width mockup, a per-pose bespoke layout, navigation nested inside an arrangement view, an arrangement view nested inside a `List`/`ScrollView`), say what's actually possible instead (size-class-driven layout, one adaptive layout via system containers, navigation wrapping the arrangement view) rather than only stopping the idea.
- **Numbers and rules are load-bearing.** Where a reference gives a specific constraint (reserved regions, vertical-axis item ordering, exact size-class matrix, "don't override default bar placement"), treat it as a hard check, not a rough guideline.

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

## Sourcing — HIG page + Apple's own Tech Talks

The HIG "Designing for iPhone Duo" page (added September 9, 2026) covers the design-pattern level. The engineering-level detail across `xcode-development.md`, `vertical-toolbars.md`, and `camera.md` comes from six of Apple's own Sept 2026 Tech Talks, reviewed directly (transcript + on-screen code): "Design for iPhone Duo," "Prepare Your App for iPhone Duo," "Raise the Bar with iPhone Duo," "Strike a Pose with Adaptive Layouts on iPhone Duo," "Leverage Multiple Displays and Scenes on iPhone Duo," and "Build a Great Camera Experience for iPhone Duo" — the full citation list is at the bottom of `xcode-development.md`. This is primary-source material (Apple's own words and code), which is why this skill treats it as higher-confidence than the general web research that seeded the first draft — but it still describes **pre-release tooling** (Xcode 27.1 is beta, not GA), so treat exact API signatures as subject to change, not a stable contract.

A direct check of Designing for iOS, Layout, Multitasking, and Live Activities (separate, older HIG pages) found no Duo-specific updates, and a dedicated `arrangement-views` HIG page does not exist (its content lives only inside the Duo page). If a question needs Duo-specific detail not present anywhere in this skill, say it isn't confirmed yet rather than inventing it.

## Coverage gaps — do not fabricate

**Treat the following as explicitly unstable/unconfirmed** (each is flagged in detail in its file, don't smooth over the caveat when answering a question that touches it):
- Xcode 27.1 / iOS 27.1 SDK (the version that adds Duo support) is a **beta, not yet GA**, per Apple's own developer hub. See [references/xcode-development.md](references/xcode-development.md).
- **`UIRequiresFullScreen` has one primary-source statement and one unverified secondary claim that conflict** — presented side by side in xcode-development.md rather than resolved in the skill's favor, since resolving it wrongly would be worse than leaving it open.
- Exact **inner-display native pixel resolution** is not published on any developer.apple.com API/points-pixels reference table — the apple.com marketing specs page states 1878×2670px, used in [references/device-specs.md](references/device-specs.md), but not yet cross-confirmed against an official developer table (none exists yet for Duo).
- **App Store Connect cannot yet accept iPhone Duo screenshot/app-preview uploads** even though the pixel specification is published ("later this year" per Apple's Sept 9, 2026 release note) — see [references/app-store-submission.md](references/app-store-submission.md).
- The **App Review Guidelines contain zero mentions** of Duo/foldable/hinge/dual-display as of this research — don't assert a specific Duo design choice "will pass/fail review" as if a citable rule exists yet.

Separately, Apple restructured the rest of the HIG site in 2025: **Haptics, Camera (HIG page), Lock Screen, Always-On Display, StandBy, Home Screen Quick Actions** no longer have independently retrievable dedicated pages (some content survives folded into the Widgets page — see references/notifications-widgets.md). Note this is about the general *Camera HIG page* being gone — Duo's camera *API* detail is well covered in [references/camera.md](references/camera.md) via the Tech Talk, a different source entirely.

Reference content here is summarized/paraphrased from Apple's public HIG, developer Tech Talks, and App Store Connect help pages as of September 2026, for internal design-feasibility use — always treat Apple's live pages as the authoritative source of truth if a decision is high-stakes or the guidance may have since changed.
