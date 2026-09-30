# Windows Autopilot Device Preparation (New Experience)

## Objective

This project explores Windows Autopilot device preparation, Microsoft's newer, faster provisioning experience for Entra joined-only devices, and compares it directly against the traditional Autopilot flow from Microsoft Entra Joined/Cloud-Only Windows-11 Autopilot.

### Skills Learned

- Configuring an Autopilot device preparation policy (apps, scripts, assignment)
- Understanding the no-pre-registration enrollment model
- Evaluating deployment speed and admin overhead trade-offs between the two Autopilot models

### Tools Used

- Microsoft Intune / Endpoint Manager admin center — Autopilot device preparation (Preview)
- Microsoft Entra ID groups

## Steps

#### 1. Create a Device Preparation Policy

Built a policy under Devices → Enrollment → Windows Autopilot → Device preparation, tagging targeted devices `GT-DevicePrep-Pilot` and assigning the policy to `SG-Win11-DevicePrep-Pilot`.

<img width="800" height="450" alt="image" src="docs/img/01-device-prep-policy.png" />

*Ref 1: Device preparation policy*

#### 2. Assign the Existing App Set to the Policy

Attached the same Store app and custom Win32 app built in [Intune Application Deployment](../02-Intune-Application-Deployment) to the preparation policy's Apps and scripts tab, so the minimum needed set installs before the user reaches the desktop, with the rest applying in the background afterward — no new packaging work was needed.

<img width="800" height="450" alt="image" src="docs/img/02-apps-assigned.png" />

*Ref 2: App assignment in device preparation*

#### 3. Enroll a Device With No Pre-Registration

Booted a factory-reset device straight to OOBE with no hardware hash imported beforehand — the device registers itself against `SG-Win11-DevicePrep-Pilot` the moment a permitted user signs in, unlike the traditional Autopilot flow.

<img width="800" height="450" alt="image" src="docs/img/03-hashless-enrollment.png" />

*Ref 3: Hashless enrollment*

#### 4. Comparison — Traditional Autopilot vs. Device Preparation

| Aspect | Traditional Autopilot | Device Preparation (this project) |
|---|---|---|
| Pre-registration required | Yes — hardware hash imported ahead of time | No — device registers itself at first sign-in |
| Join type support | Entra joined, Hybrid joined, Autopilot for existing devices | Entra joined only |
| Typical time to desktop | Longer — full app/policy set applies before unlock | Faster — minimal Tier-1 set applies before unlock, rest in background |
| Best fit | Planned corporate rollouts, hybrid environments | Frontline/shared devices, unplanned replacements, retail |

*Ref 4: Provisioning model comparison*

## About

Microsoft's newer, hash-free Autopilot provisioning model, built and benchmarked directly against the traditional flow.
