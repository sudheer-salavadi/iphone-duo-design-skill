# iPhone Duo — Physical Device Specs
Sources: https://www.apple.com/iphone-duo/specs/ (marketing tech-specs page) and, for screenshot-canvas pixel dimensions, https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications/ — researched 2026-09-12, three days after the device's Sept 9, 2026 announcement (ships ~2026-10-23).

**A note on confidence**: these numbers come from Apple's consumer marketing specs page and App Store Connect's developer help pages — not from a developer.apple.com API/points-and-pixels reference table, which as of this research does not yet list iPhone Duo (the legacy "iOS Device Compatibility Displays" reference stops at iPhone X-era devices). Treat the figures below as high-confidence but not yet cross-confirmed against an official developer points/pixels table — re-check closer to Xcode 27.1 GA, when Apple typically publishes that table.

## Displays

**Capabilities**
- Inner (folding) display: 7.6" diagonal, all-screen OLED, **1878 × 2670 px, 430 ppi** (native panel resolution, per apple.com specs page).
- Outer display: 5.4" diagonal, all-screen OLED, **1398 × 2034 px, 460 ppi** (native panel resolution, per apple.com specs page).
- Both: Super Retina XDR OLED, ProMotion up to 120Hz, Always-On.

**Limitations / lower-confidence items (flagged, not to be treated as settled)**
- 3000-nit peak outdoor brightness, a 10-ultrathin-layer inner-panel construction, and a custom nano-texture-style anti-glare finish are reported only by a secondary source (macobserver.com), not independently confirmed against the apple.com specs page text directly — treat as **lower confidence**.
- The claim that both displays share the same aspect ratio (so content scales proportionally between them) is also secondary-source-only — **lower confidence**.
- **App Store Connect's screenshot-canvas dimensions differ from the native panel pixel counts above**: the *inner-display screenshot* requirement is 2007 × 2853 px (portrait) — not 1878×2670. This is not necessarily a contradiction: Apple's screenshot specs have historically been point-based rendering targets rather than literal native-panel pixel counts for other devices too. Don't conflate "native panel resolution" (this file) with "App Store Connect screenshot canvas size" (see [app-store-submission.md](app-store-submission.md)) — they are two different numbers for two different purposes.
- **Hinge angle range** (degrees) is not documented anywhere found — NOT CONFIRMED.

**Design implications**
- Use the native panel pixel/ppi numbers above for asset-resolution planning (image export scale, photography crops) — use the separate App Store Connect screenshot dimensions only for actual store-listing screenshot assets, not as a proxy for on-device rendering resolution.
- Because neither number has been cross-confirmed against an official developer points-per-inch/scale-factor table yet, avoid hardcoding pixel math into a design spec as if it were final — frame it as "best available as of Sept 2026, re-verify before production asset export."

---

## Physical Dimensions & Weight

**Capabilities**
- Open (unfolded): 6.48 in (164.6 mm) W × 4.64 in (117.8 mm) H × 0.21 in (5.2 mm) D.
- Closed (folded): 3.31 in (84.1 mm) W × 4.64 in (117.8 mm) H × 0.44 in (11.3 mm) D.
- Weight: 8.96 oz (254 g) — identical whether open or closed (same physical mass).

**Design implications**
- 254g is meaningfully heavier than a standard iPhone (typically ~170-230g depending on model) — one-handed reachability assumptions carried over from standard-iPhone design work (see [layout-navigation.md](layout-navigation.md)) may need re-validation specifically for one-handed *closed-device* use, given the added weight and the narrower (3.31") closed-state width.

---

## Chip, Storage, Durability, Battery

**Capabilities**
- Chip: A20 Pro — 6-core CPU (up to 20% faster than A19 Pro), 7-core GPU (up to 40% faster), dual 16-core Neural Engine, 2-nanometer process.
- Storage: 256GB, 512GB, 1TB, 2TB.
- Finishes: Night Sky, Star White (titanium foldable body).
- Durability: IP68 (max depth 6m, up to 30 minutes, per IEC 60529).
- Battery: typical use up to 24 hours; video playback up to 44 hours using the outer display, or 31 hours using the inner display.
- Connectivity: eSIM only — no physical SIM tray.
- Camera (for reference, not a design-guideline detail): 48MP Fusion Main (26mm, ƒ/1.6, sensor-shift OIS), 2x Telephoto (52mm, ƒ/1.6).

**Design implications**
- The **13-hour battery-life gap between outer-display and inner-display video playback (44hr vs 31hr)** is a concrete, user-facing tradeoff: a design that defaults to opening the device for a task that could just as well run on the outer display is a real battery cost, not a nitpick — default to the outer/closed experience for tasks that don't need the larger canvas.
- eSIM-only is a carrier/onboarding-flow consideration (no physical-SIM setup path to design for), not a screen-layout one.
