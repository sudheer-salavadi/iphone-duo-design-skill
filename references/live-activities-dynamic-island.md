# Live Activities & Dynamic Island
Source: https://developer.apple.com/design/human-interface-guidelines/live-activities

**Capabilities**
- Surfaces across iPhone/iPad Lock Screen + Home Screen banner, Dynamic Island, Mac menu bar, Apple Watch Smart Stack, and CarPlay Dashboard — one Live Activity implementation reaches many surfaces.
- Dynamic Island presentation states: **compact** (single active activity, two elements flanking camera), **minimal** (multiple activities active, shrinks to attached/detached dot), **expanded** (touch-and-hold reveal, full info).
- StandBy: minimal presentation scales 2x on tap, becomes a full Lock-Screen-style layout.
- Updatable via push notification; supports limited interactive elements (buttons/toggles) for simple actions (music playback, workout controls).

**Limitations (hard numeric constraints)**
- Best suited to activities lasting ≤8 hours; must end immediately when the task/event actually ends (custom dismissal window ~15-30 min after completion).
- Layout sizes (points): Compact 62.33×36.67 (Pro Max) / 52.33×36.67 (standard); Minimal width 36.67-45; Expanded height 84-160; Lock Screen height 84-160.
- Dynamic Island width: 250pt (Pro Max models) / 230pt (standard); expanded view 408pt / 371pt.
- watchOS Smart Stack: 152×69.5pt (40mm) up to 191×81.5pt (49mm). CarPlay: 240×78pt / 240×100pt / 170×78pt.
- Update-triggered animations capped at 2 seconds.
- No ads/promotions permitted in a Live Activity.
- Limit to single interactive element where possible — this is not a general-purpose mini-app surface.

**Design implications**
- A Live Activity concept for something open-ended/long-running (e.g., "always-on companion") is not feasible — design around bounded, ≤8hr tasks with a clear end state (delivery tracking, live sports score, timer, workout).
- Text must be large and medium-weight-or-heavier for glanceability at Dynamic-Island scale — dense/small text mockups won't survive real rendering.
- Because interactive surface is minimal (1 element ideally), don't design multi-control interfaces here; route anything complex into the full app via tap-to-open.
- Alerting should be reserved for genuinely essential updates — avoid "notify on every update" design patterns that duplicate push notifications.

**Coverage gap**: dedicated Lock Screen / Always-On Display / StandBy HIG pages 404 on the current site; StandBy specifics above are the only ones recoverable, via this page. Don't assume more precision than what's listed here for AOD-specific rendering.
