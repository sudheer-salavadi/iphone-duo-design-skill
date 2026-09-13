# iPhone Duo Design Skill — Claude Code

A [Claude Code](https://claude.com/claude-code) skill for designing and critiquing UX/product design work for **iPhone Duo**, Apple's foldable iPhone (dual displays, hinge, announced September 9, 2026, shipping ~October 23, 2026). It grounds design decisions in real technical capabilities and constraints spanning the Human Interface Guidelines, physical device specs, Xcode/API-level engineering feasibility (sourced directly from Apple's own Sept 2026 developer Tech Talks, including their on-screen code), and App Store Connect submission requirements — device poses, reserved camera/folding regions, the displacement design pattern, split views, arrangement views, the full vertical toolbar/tab-bar API, dual front-camera APIs, exact screen resolutions, and screenshot dimensions — and explicitly calls out what's different from standard iPhone design versus what carries over unchanged (accessibility, notifications, widgets, Live Activities, privacy, commerce), so a design idea can be scoped and checked for feasibility before it's built.

This is a research/reference compilation for personal design work, not an official Apple product. Content is summarized/paraphrased from Apple's public Human Interface Guidelines, Apple's own Sept 2026 developer Tech Talks (reviewed directly, including their code samples), and App Store Connect help pages as of September 2026 — always treat Apple's live pages as the authoritative source for anything high-stakes, since guidance changes over time (Duo coverage in particular is brand new, and its Xcode/SDK tooling is still in beta). All rights to the original content belong to Apple.

## What's inside

- `SKILL.md` — the skill definition Claude Code loads: how to use the material, and a table of contents into the reference files.
- `references/iphone-duo.md` — the primary HIG reference: iPhone Duo's anatomy, device poses, reserved regions, the **displacement** design pattern, dynamic layouts, split views, arrangement views, vertical toolbar/tab-bar rules, sheets, system multitasking/Picture-in-Picture, and games — split into Capabilities / Limitations / Design implications, cited to source.
- `references/standard-iphone-vs-duo.md` — the scoping guide: what carries over from standard iPhone unchanged, a side-by-side differences table, and scoping questions to decide how much Duo-specific work a given feature actually needs.
- `references/device-specs.md` — physical specs: screen sizes/resolutions/ppi for both displays, physical dimensions/weight, chip/storage/battery/durability.
- `references/xcode-development.md` — core engineering feasibility: exact size-class matrix, screen/display access, safe areas, `UIRequiresFullScreen`, split-view multitasking and multi-window rules, scene accessories, and the hinge/reserved-region/arrangement-view APIs — with real code from Apple's own Tech Talks.
- `references/vertical-toolbars.md` — the full vertical toolbar/tab-bar/sheet API surface: opting in, item ordering, axis-behavior overrides, badges, overflow/compression configuration, and how to disable it — with exact code from Apple's "Raise the Bar with iPhone Duo" talk.
- `references/camera.md` — the dual front-camera API surface: virtual-vs-individual camera capability tradeoffs (resolution/fps/depth), the Direction Coordinator (cameras aren't fixed-facing on a foldable device), and preview/rotation polish — from Apple's "Build a Great Camera Experience for iPhone Duo" talk.
- `references/app-store-submission.md` — App Store Connect screenshot requirements (exact pixel dimensions, and that asset upload isn't live yet) and confirmation that the App Review Guidelines currently have zero Duo-specific language.
- `references/` (remaining files) — the standard iOS baseline that still applies to Duo unless noted: layout & navigation, gestures & input, multitasking, Live Activities/Dynamic Island, notifications & widgets, accessibility, visual system, sensors, privacy/permissions, commerce/collaboration/Siri.

## Install

**As a personal skill** (available in every project):
```bash
git clone https://github.com/sudheer-salavadi/iphone-duo-design-skill.git ~/.claude/skills/iphone-duo-design
```

**As a project-scoped skill** (only in one repo):
```bash
git clone https://github.com/sudheer-salavadi/iphone-duo-design-skill.git .claude/skills/iphone-duo-design
```

Restart Claude Code (or start a new session) and the skill will appear in the available-skills list. Invoke it explicitly with `/iphone-duo-design`, or just ask an iPhone Duo design/feasibility question — Claude will pick it up from the skill description.

## Sourcing

The HIG "Designing for iPhone Duo" page (added September 9, 2026) covers the design-pattern level. The engineering-level detail comes from six of Apple's own Sept 2026 Tech Talks, reviewed directly — transcript and on-screen code both — rather than secondhand summaries: "Design for iPhone Duo," "Prepare Your App for iPhone Duo," "Raise the Bar with iPhone Duo," "Strike a Pose with Adaptive Layouts on iPhone Duo," "Leverage Multiple Displays and Scenes on iPhone Duo," and "Build a Great Camera Experience for iPhone Duo" (full citation list at the bottom of `references/xcode-development.md`). This is why API names/code in this skill are higher-confidence than a typical secondary-source web summary — but the underlying tooling (Xcode 27.1) is still pre-GA, so treat exact signatures as subject to change before release.

## Known gaps

The Designing for iPhone Duo HIG page is brand new and, as of this research, the *only* HIG page with Duo-specific content — Designing for iOS, Layout, Multitasking, and Live Activities were directly checked and found to have no Duo-specific updates, and a dedicated `arrangement-views` HIG page doesn't exist (its content lives only inside the Duo page).

Beyond the HIG: **Xcode 27.1 / iOS 27.1 SDK, which add Duo support, are still an unreleased beta** as of this research ("coming later this month" per Apple's developer hub). `UIRequiresFullScreen` has **one primary-source statement (Apple's own talk) and one unverified secondary claim that conflict with it**, presented side by side in `references/xcode-development.md` rather than resolved in either direction. The exact **inner-display native pixel resolution** isn't published on any developer.apple.com API/points-pixels reference table (only Apple's marketing specs page, used here). **App Store Connect can't yet accept iPhone Duo screenshot uploads** even though the pixel spec is published. And the **App Review Guidelines have zero Duo-specific language** as of this research. See `references/xcode-development.md` and `references/app-store-submission.md` for the full caveats.

Separately, Apple's 2025 HIG restructure removed a few previously-standalone pages (Haptics, the Camera HIG page, Lock Screen, Always-On Display, StandBy, Home Screen Quick Actions) as independent pages; some content survives folded into other pages (noted in the relevant reference file). This doesn't affect Duo's camera *API* coverage, which comes from a dedicated Tech Talk — see `references/camera.md`.
