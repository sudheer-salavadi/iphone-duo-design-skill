# iPhone Duo — App Store Connect Submission & Review
Sources: https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications/, App Store Connect release notes (Sept 9, 2026 entry), https://developer.apple.com/app-store/review/guidelines/ — researched 2026-09-12.

## Screenshots & App Previews

**Capabilities**
- iPhone Duo has its **own distinct App Store Connect screenshot category**, separate from the existing "6.9" Display" category (iPhone Air / 18 Pro Max / etc.). It requires **two sets** of screenshots — one per display:
  - **Outer display**: 1398 × 2034 px (portrait), 2034 × 1398 px (landscape)
  - **Inner display**: 2007 × 2853 px (portrait), 2853 × 2007 px (landscape)
- General format rules (not Duo-specific): .jpeg/.jpg/.png, flattened (no alpha/transparency), RGB color space, 1–10 screenshots per size category.

**Limitations — the most important fact in this file**
- **The screenshot *specification* exists, but the ability to *upload* iPhone Duo screenshots/app previews in App Store Connect is not yet live.** Apple's Sept 9, 2026 release note states (near-verbatim): *"The app preview and screenshot specifications for iPhone Duo, iPhone 18 Pro Max, iPhone 18 Pro, Apple Watch Ultra 4, and Apple Watch Series 12 have been added. Support for uploading assets for iPhone Duo in App Store Connect will be available later this year."*
- As of Sept 12, 2026, a developer cannot yet submit Duo-specific screenshots even though the required dimensions are published.
- A general fallback/scaling behavior (App Store Connect filling a missing size category by scaling from another category) likely applies here too, consistent with existing behavior for other device categories — but the specific claim that it falls back to "6.5" display" screenshots could not be independently re-confirmed verbatim; treat only the general mechanism as likely, not that specific source category.
- The inner-display screenshot canvas (2007×2853px) does not match the native inner-panel resolution (1878×2670px, per [device-specs.md](device-specs.md)) — this is expected (Apple's screenshot specs are historically point-based render targets, not literal native pixel counts), not an error, but don't conflate the two numbers.

**Design implications**
- **Don't scope Duo-specific App Store screenshot/marketing asset production as a near-term deliverable** — the pixel dimensions are known and can be designed against today, but there is nowhere to upload them yet. Flag this explicitly if a launch-marketing plan assumes Duo screenshots will be live in the store at a specific date before "later this year."
- When that asset production does happen, design outer- and inner-display screenshots as two genuinely different assets (different aspect ratios, 1398×2034 vs 2007×2853), not one image scaled to fit both.

---

## App Review Guidelines

**Confirmed finding**: the App Review Guidelines (developer.apple.com/app-store/review/guidelines/) were checked twice, independently, for any mention of "iPhone Duo," "foldable," "fold," "dual display," "hinge," or "pose" — **zero matches**, including in the Design, Performance, and Hardware Compatibility sections.

**Design implications**
- There is currently **no published App Review policy specific to iPhone Duo** — device-specific design guidance lives only in the Human Interface Guidelines (see [iphone-duo.md](iphone-duo.md)) and in Apple's developer Tech Talks (see [xcode-development.md](xcode-development.md)), not in the enforceable Review Guidelines text itself.
- Don't assert to a stakeholder that a specific Duo design choice "will pass/fail review" as if there's a citable review rule for it yet — the only citable source right now is HIG guidance (a strong signal reviewers likely follow informally) and general, non-Duo-specific review rules (e.g. general layout/functionality requirements) already covered elsewhere in this skill. Frame Duo-specific review risk as "no documented rule yet, but HIG non-compliance is still a reasonable risk signal," not as a confirmed review gate.

---

## Metadata & Device-Support Declarations

**NOT CONFIRMED — searched, not found:**
- No new required/optional App Store Connect metadata field specific to iPhone Duo in the App Information section.
- No distinct "device support" declaration/checkbox for foldable/dual-display support (unlike, say, how Apple Watch or iPad support is explicitly declared).
- No minimum iOS/iPadOS version gate specifically tied to enabling iPhone Duo support in a listing.

**Design implications**
- Given the "asset upload support coming later this year" note on screenshots, it's plausible metadata/device-declaration changes are also pending and simply undocumented yet — but this is speculation, not a finding. Don't design a submission checklist item around a metadata field that hasn't been confirmed to exist.

---

## TestFlight / Distribution Testing

**NOT CONFIRMED — searched, not found:** no documentation addresses whether a physical iPhone Duo unit is required for TestFlight beta testing of dual-display behavior, versus whether the (not-yet-GA) Simulator support is considered sufficient, or any iPhone Duo-specific TestFlight build/upload requirement.

**Design implications**
- Don't assume TestFlight-based user testing of Duo-specific flows is available yet — treat physical-device availability and Simulator-beta-readiness as the current gating factors for any real-device validation plan, and revisit once Xcode 27.1 ships.
