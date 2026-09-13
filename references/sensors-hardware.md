# Sensors & Hardware Capabilities

## Motion (Gyroscope & Accelerometer)
Source: https://developer.apple.com/design/human-interface-guidelines/gyro-and-accelerometer

**Capabilities**
- Real-time motion data available via Core Motion on iOS/iPadOS/watchOS, and via Siri Remote on tvOS — usable for fitness tracking, gameplay, and similar "tangible benefit" scenarios.

**Limitations**
- Explicit permission request with a rationale string is required before first access.
- Guidance explicitly discourages using accelerometer/gyroscope for *direct UI manipulation* outside active gameplay, citing (a) difficulty of precise replication, (b) exclusion of users with motor limitations, and (c) battery drain.

**Design implications**
- A "tilt your phone to navigate the UI" concept outside a game context is against guidance on three separate grounds (precision, accessibility, battery) — treat this as a hard steer-away, not a stylistic option.
- Motion-based features need a stated "tangible benefit" framing to justify the permission prompt and battery cost in the design rationale.

---

## Maps
Source: https://developer.apple.com/design/human-interface-guidelines/maps

**Capabilities**
- Standard/satellite/hybrid views; default (saturated) and muted (desaturated, info-dense) emphasis styles; clustering of overlapping points of interest that progressively expand on zoom.
- Custom annotations (tint color, 2-3 character string icons, or images) and two overlay levels (above-roads vs. above-labels).
- Place cards in multiple styles (automatic, callout, caption, sheet).
- Indoor maps support: floor pickers, room/kiosk overlays, progressive detail by zoom.

**Limitations**
- Apple logo + legal attribution have fixed minimum padding (7pt sides, 10pt above/below) and must stay fixed to the map — cannot be hidden/permanently covered.
- Guidance explicitly says not to visually clone Apple Maps' own styling in a custom map skin.

**Design implications**
- Any map mockup must reserve fixed padding for the Apple logo/legal link — it cannot be design-cropped out.

---

**Coverage gap**: a dedicated Haptics HIG page 404s on the current site (2025 restructure) and could not be independently retrieved — don't state specific haptic pattern names/timing as confirmed HIG guidance, flag as unverified if asked. The general Camera HIG page is also gone (permission behavior is partially covered under [privacy-permissions.md](privacy-permissions.md)), but iPhone Duo's actual camera *API* surface — the dual front cameras, the Direction Coordinator, preview/rotation handling — is well covered separately in [camera.md](camera.md), sourced from Apple's own camera Tech Talk rather than the (gone) HIG page.
