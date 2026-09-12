# Accessibility Constraints
Source: https://developer.apple.com/design/human-interface-guidelines/accessibility

**Capabilities**
- VoiceOver screen reader, Dynamic Type scaling, Voice Control, Switch Control, Assistive Access, Reduce Motion — all system-level and free to integrate with via standard components.

**Limitations (concrete, testable numbers)**
- Dynamic Type: must support up to **200% enlargement on iOS/iPadOS/visionOS** (140% on watchOS).
- Default/minimum font sizes: iOS/iPadOS 17pt default / 11pt minimum; macOS 13/10pt; tvOS 29/23pt; visionOS 17/12pt; watchOS 16/12pt.
- Color contrast (WCAG AA): body text ≤17pt needs **4.5:1**; text ≥18pt or bold needs **3:1**.
- Minimum control/hit-target size: iOS/iPadOS **44×44pt** (28×28pt absolute minimum); macOS 28×28 (20×20 min); tvOS 66×66 (56×56 min); visionOS 60×60 (28×28 min); watchOS 44×44 (28×28 min).
- Spacing: ~12pt padding around bezeled controls, 24pt around non-bezeled elements.

**Design implications**
- Any control sized below the platform's stated minimum (44×44pt on iPhone) is a concrete accessibility feasibility failure, not a stylistic judgment call — flag it explicitly.
- Text/background color pairs must be checked against the 4.5:1 / 3:1 thresholds above — "looks readable" is not sufficient.
- A UI must survive 200% Dynamic Type scaling without breaking layout — design review should include a "what does this look like at max Dynamic Type" pass, not just default type size.
- Color-only signifiers (e.g., red = error) must be paired with an icon/shape — color-alone encoding fails accessibility guidance.
- Must respect "Reduce Motion" — heavy parallax/animation-driven concepts need a reduced-motion fallback design, not just an on/off toggle bolted on later.
