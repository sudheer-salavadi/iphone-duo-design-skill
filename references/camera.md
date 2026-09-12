# iPhone Duo — Dual Front-Camera APIs
Source: Apple's Sept 2026 Tech Talk "Build a Great Camera Experience for iPhone Duo" (Tun, Camera Software team) — transcript reviewed directly, including its on-screen code samples.

iPhone Duo is the first iPhone with **two front cameras** — both square-sensor, ultrawide field of view. The **outer** ultrawide camera sits on the outer display; the **inner** ultrawide camera is the first under-display camera on iPhone, on the inner display. This file covers the capture APIs; see [xcode-development.md](xcode-development.md) for the Camera Capture Accessory (showing supplementary UI, like a teleprompter, on the outer display while the main camera UI runs on the inner display).

> Same pre-release caveat as the rest of this skill: iOS 27.1 SDK territory, still beta as of this research.

## Virtual Front Camera vs. Individual Cameras — a real capability tradeoff

**Capabilities**
- Using the standard `AVCaptureDevice.DiscoverySession` with position `.front` and a wide/ultrawide device type automatically discovers a new **virtual front camera** — an `AVCaptureDevice` that transparently switches between the physical outer and inner cameras based on open/closed state (open → inner camera in use; closed → outer camera in use). This is the easy, mostly-automatic path.
- Accessing the cameras **individually** via their own device types gives full per-camera capability:
  - `builtInOuterUltraWideCamera` — up to **4K resolution, 120fps**.
  - `builtInInnerUltraWideCamera` — up to **1080p resolution, 60fps**.

**Limitations**
- The virtual front camera only exposes the capabilities **common to both** physical cameras — capped at **1080p / 60fps**, and it does **not support depth data at all** (depth is only available when accessing an individual physical camera directly).
- Using the individual camera device types means the app is responsible for handling the camera switch itself when the device opens/closes — via the Direction Coordinator (below), not automatically.

**Design implications**
- A camera feature that needs 4K/120fps or depth data (e.g. a portrait-mode-style effect, a high-frame-rate slow-mo mode) **cannot** use the convenient virtual-camera path — it must be scoped around individual-camera access and the added engineering work of manual switching.
- A simple camera feature with modest resolution needs (e.g. a basic video-call front camera) is a good fit for the virtual camera — flag the resolution/depth tradeoff explicitly if a design later asks for "just a bit more quality" or a depth-based effect, since that requires a different technical approach, not a setting toggle.

---

## Direction Coordinator — cameras don't always face the user

**The core problem this API solves**: `AVCaptureDevice.position` is a fixed property (`.back`/`.front`), but on Duo, a "front" camera doesn't always face the user — it depends on which display is active and how the device is currently held/folded:
- Streaming from the outer front camera while looking at the **inner** display means that camera is facing **away** from the user.
- Closing the device swings that same outer camera to face the user again.
- Flipping the device open onto its outer display makes the **rear** camera (and the outer front camera) **both** forward-facing simultaneously.

**Capabilities**
```swift
directionCoordinator = AVCaptureDeviceDirectionCoordinator(
    view: view,
    deviceTypes: [
        .builtInOuterUltraWideCamera,
        .builtInInnerUltraWideCamera,
        .builtInDualWideCamera,
    ],
    changeHandler: { [weak self] map in
        self?.updateCameraSession(map)
    }
)
```
- The coordinator reports which cameras are **forward-facing** vs. **backward-facing** *relative to a specific UI view* — not a single device-wide truth.
- **Dual-display simultaneous use** (e.g. a group video call, or showing a child what you're filming while you photograph them): use **two separate UI views** (one per display) and **a separate Direction Coordinator per view** — each coordinator reports positions relative to its own view, so it's normal and expected for the two coordinators to disagree at the same moment (e.g. outer view's coordinator says rear=forward while inner view's coordinator says rear=backward). This dual-display setup is enabled via the Scene Accessories API — see [xcode-development.md](xcode-development.md).

**Limitations**
- Because the coordinator is tied to a view, it is **isolated to the main actor**. The change handler should **not** call AVFoundation APIs directly — instead it receives an `AVCaptureDevice` **descriptor** (a main-actor-safe, `Sendable` representation with everything needed to construct a real `AVCaptureDevice`), meant to be passed to a background camera actor.

**What to do in the change handler**:
1. Reconfigure the capture session to keep streaming from the now-forward-facing camera.
2. Decide on mirroring: **mirror the preview when the rear camera is forward-facing**, for a natural selfie-camera feel.
3. Perform any UI updates the camera-direction change requires.

Reference article: "Choosing a Camera by the Direction It Faces" (Apple developer documentation).

**Design implications**
- Any camera-feature design that assumes "the front camera" is a single, always-user-facing concept is not technically accurate for Duo — the direction coordinator's per-view, dynamic model is the correct mental model, and should be named explicitly in a spec that involves flipping/folding while the camera is active.
- The dual-simultaneous-view pattern (two views, two coordinators) directly supports "show the subject a live view while filming them" concepts (interviews, vlogging, photographing kids) — this is a purpose-built pattern, not something to hand-roll.

---

## Preview Layout Polish

```swift
class AVCaptureVideoPreviewLayer {
    var videoGravity: AVLayerVideoGravity { get set }
}
```
- Use `videoGravity` to decide whether the preview fills the display or is offset with extra space around it (e.g. to group controls in the remaining area) — when streaming the rear camera's full field of view on the inner display, there's extra display space to make this choice with.

```swift
class AVCaptureDevice {
    var dynamicAspectRatio: AVCaptureDevice.AspectRatio? { get }
}
```
- When streaming from the ultrawide **front** cameras (square sensor), `dynamicAspectRatio` can select a **landscape** aspect-ratio crop of the square sensor to fill the wider inner display. Related background: "Support the Center Stage Front Camera in Your iOS App" (WWDC26).

---

## Rotation Handling

Adopt `AVCaptureDevice.RotationCoordinator` to keep the camera preview and captured photos upright — on Duo it updates automatically as the app moves between displays, so rotation handling stays consistent across both. Reference article: "Supporting Device Rotation in Your Camera App."

```swift
class AVCapturePhotoOutput: AVCaptureOutput {
    // Disable for improved performance, once RotationCoordinator is adopted —
    // this compensation is enabled by default on all front cameras on iPhone Duo.
    var isCameraSensorOrientationCompensationEnabled: Bool { get set }
}
```

**Design implication**: adopting the rotation coordinator and then disabling sensor-orientation compensation is a paired change (a performance win that only makes sense once the coordinator is in place) — flag it as a single unit of engineering work, not two independent options.
