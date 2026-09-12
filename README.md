# iPhone Duo Design Skill — Claude Code

A [Claude Code](https://claude.com/claude-code) skill for designing and critiquing UX/product design work for **iPhone Duo**, Apple's foldable iPhone (dual displays, hinge, announced September 9, 2026, shipping ~October 23, 2026). It grounds design decisions in real technical capabilities and constraints spanning the Human Interface Guidelines, physical device specs, Xcode/API-level engineering feasibility, and App Store Connect submission requirements — device poses, reserved camera/folding regions, split views, arrangement views, vertical toolbar placement, exact screen resolutions, the new hinge/reserved-region/arrangement-view APIs, and screenshot dimensions — and explicitly calls out what's different from standard iPhone design versus what carries over unchanged (accessibility, notifications, widgets, Live Activities, privacy, commerce), so a design idea can be scoped and checked for feasibility before it's built.

This is a research/reference compilation for personal design work, not an official Apple product. Content is summarized/paraphrased from Apple's public Human Interface Guidelines, Developer Documentation/Tech Talks, and App Store Connect help pages as of September 2026 — always treat Apple's live pages as the authoritative source for anything high-stakes, since guidance changes over time (Duo coverage in particular is brand new, and its Xcode/SDK tooling is still in beta). All rights to the original content belong to Apple.

## What's inside

- `SKILL.md` — the skill definition Claude Code loads: how to use the material, and a table of contents into the reference files.
- `references/iphone-duo.md` — the primary HIG reference: iPhone Duo's anatomy, device poses, reserved regions, dynamic layouts, split views, arrangement views, vertical toolbar/tab-bar rules, and games — split into Capabilities / Limitations / Design implications, cited to source.
- `references/standard-iphone-vs-duo.md` — the scoping guide: what carries over from standard iPhone unchanged, a side-by-side differences table, and scoping questions to decide how much Duo-specific work a given feature actually needs.
- `references/device-specs.md` — physical specs: screen sizes/resolutions/ppi for both displays, physical dimensions/weight, chip/storage/battery/durability.
- `references/xcode-development.md` — engineering feasibility: Xcode/Simulator support status (currently beta), the new hinge/reserved-region/arrangement-view APIs (SwiftUI + UIKit), SDK-version-dependent rendering behavior, and migration requirements for existing apps.
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

## Known gaps

The Designing for iPhone Duo HIG page is brand new and, as of this research, the *only* HIG page with Duo-specific content — Designing for iOS, Layout, Multitasking, and Live Activities were directly checked and found to have no Duo-specific updates yet, and a dedicated `arrangement-views` page doesn't exist (its content lives only inside the Duo page). Expect Apple to expand Duo coverage into other HIG pages over time; treat anything not in `references/iphone-duo.md` as unconfirmed for Duo specifically.

Beyond the HIG: **Xcode 27.1 / iOS 27.1 SDK, which add Duo support, are still an unreleased beta** as of this research ("coming later this month" per Apple's developer hub) — the new hinge/reserved-region/arrangement-view API classes are known only from Tech Talk video transcripts, with no confirmed standalone reference pages yet. The exact **inner-display native pixel resolution** isn't published on any developer.apple.com page found (only Apple's marketing specs page, used here). **App Store Connect can't yet accept iPhone Duo screenshot uploads** even though the pixel spec is published. And the **App Review Guidelines have zero Duo-specific language** as of this research. See `references/xcode-development.md` and `references/app-store-submission.md` for the full caveats.

Separately, Apple's 2025 HIG restructure removed a few previously-standalone pages (Haptics, Camera, Lock Screen, Always-On Display, StandBy, Home Screen Quick Actions) as independent pages; some content survives folded into other pages (noted in the relevant reference file).
