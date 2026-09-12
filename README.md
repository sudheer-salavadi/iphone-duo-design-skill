# iOS HIG Feasibility — Claude Code Skill

A [Claude Code](https://claude.com/claude-code) skill that grounds iPhone/iOS product design and UX work in Apple's Human Interface Guidelines: hard technical constraints (tap target sizes, type scaling, contrast ratios, Live Activity/widget dimensions, character limits, timing windows) and platform capabilities (Live Activities, Dynamic Island, widgets, App Clips, notifications, App Intents/Siri, SharePlay, Sign in with Apple, In-App Purchase), organized so a design idea can be checked for technical feasibility before it's built.

This is a research/reference compilation for personal design work, not an official Apple product. Content is summarized/paraphrased from Apple's public [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) as of September 2026 — always treat Apple's live HIG pages as the authoritative source for anything high-stakes, since guidance changes over time. All rights to the original guideline content belong to Apple.

## What's inside

- `SKILL.md` — the skill definition Claude Code loads: how to use the material, and a table of contents into the reference files.
- `references/` — ten topic files (layout & navigation, gestures & input, multitasking, Live Activities/Dynamic Island, notifications & widgets, accessibility, visual system, sensors, privacy/permissions, commerce/collaboration/Siri), each split into **Capabilities**, **Limitations**, and **Design implications** with the source HIG URL cited.

## Install

**As a personal skill** (available in every project):
```bash
git clone https://github.com/sudheer-salavadi/ios-hig-design-skill.git ~/.claude/skills/ios-hig-feasibility
```

**As a project-scoped skill** (only in one repo):
```bash
git clone https://github.com/sudheer-salavadi/ios-hig-design-skill.git .claude/skills/ios-hig-feasibility
```

Restart Claude Code (or start a new session) and the skill will appear in the available-skills list. Invoke it explicitly with `/ios-hig-feasibility`, or just ask about an iPhone design/feasibility question — Claude will pick it up from the skill description.

## Known gaps

Apple restructured the HIG site in 2025. A few previously-standalone pages (Haptics, Camera, Lock Screen, Always-On Display, StandBy, Home Screen Quick Actions) no longer resolve as independent pages; some of their content survives folded into other pages (noted in the relevant reference file). Where the reference files don't have a confirmed number or rule, they say so rather than guessing.
