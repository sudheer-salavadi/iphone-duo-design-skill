# Privacy & Permissions UI

## Privacy
Source: https://developer.apple.com/design/human-interface-guidelines/privacy

**Capabilities**
- One custom "pre-permission" explainer screen is allowed before the system permission alert, to give context.
- iOS/iPadOS/watchOS Location Button gives one-time, lightweight location access without a full permanent-permission grant.

**Limitations**
- Permission purpose strings must be specific, active-voice, and justify the actual use — vague strings ("for a better experience") are explicitly called out as non-compliant examples.
- Pre-permission screens cannot include a cancel/dismiss path that bypasses the real system alert, cannot use "Allow"-like button language, and cannot visually mimic the system alert.
- App Tracking Transparency: apps cannot offer compensation/incentives for granting tracking permission, and cannot gate functionality/content on the user granting tracking permission.
- Passwords must never be stored in plain text; system keychain is the required storage mechanism for sensitive credentials.
- visionOS: back camera exposes no live feed to apps by default (blank input); front camera access requires explicit permission and is limited to spatial personas use.

**Design implications**
- Any custom pre-permission screen design must be reviewed against the "no bypass, no mimicry, no incentive" rules — these are App Store review-enforced, not just stylistic guidance.
- A monetization or feature-gating flow built around "must allow tracking to continue" is explicitly disallowed — remove/redesign this pattern rather than assume it will pass review.
- Sensitive data storage (passwords, tokens) must route through Keychain in any technical plan tied to a login/auth design.

---

## Sign in with Apple
Source: https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple

**Capabilities**
- Face ID/Touch ID/Optic ID-backed sign-in with built-in 2FA; private relay email so users can share a proxy address instead of their real one.
- Three approved button copy variants: "Sign in with Apple," "Sign up with Apple," "Continue with Apple" (watchOS: "Sign in" only).

**Limitations**
- Button minimum size: 140×30pt with margin equal to 1/10 of button height.
- Custom buttons must keep exact approved title text, uniform black/white logo+text (no custom colors), and Apple-supplied logo artwork only (no custom-drawn Apple logo, no cropping).
- **App Review evaluates all custom Sign in with Apple buttons** — this is an explicit App Store review gate tied directly to a UI element's design.
- Apps must never request the private relay user's real email, and must never require a password (Sign in with Apple is passwordless by definition).

**Design implications**
- Custom-styled Sign in with Apple buttons are real but constrained — treat the button as "themeable chrome around a fixed logo+copy," not a fully custom design surface, and flag review risk if constraints are violated.
- If Sign in with Apple is offered, delay any additional profile/data collection until after users have experienced value in the app — asking for extra data immediately at signup contradicts stated best practice.
