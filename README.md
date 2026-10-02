# Windows Autopilot Device Preparation (New Experience)

## Objective

Set up Windows Autopilot device preparation, Microsoft's newer provisioning model for Entra joined Windows 11 devices, and compare it with the traditional Autopilot flow.

### Skills Learned

- Creating a device group with the Intune Provisioning Client as owner
- Configuring an Autopilot device preparation policy (settings, apps, scripts, assignment)
- Understanding enrollment time grouping and the no-pre-registration model
- Evaluating deployment speed and admin overhead against traditional Autopilot

### Tools Used

- Microsoft Intune admin center: Windows Autopilot device preparation policies
- Microsoft Entra ID groups

## Steps

#### 1. Create the Groups

- **User group** (`SG-Win11-DevicePrep-Users`): users allowed to deploy devices. This is the policy assignment.
- **Device group** (`SG-Win11-DevicePrep-Devices`): Security group with **Assigned** membership, left empty. Add the **Intune Provisioning Client** service principal (AppId `f1346770-5b25-470b-88bd-d5744ab7952c`, in some tenants named *Intune Autopilot ConfidentialClient*) as **Owner**. Devices are added to this group automatically during enrollment.

<img src="docs/img/01-groups.png" alt="Groups" width="800" />

*Ref 1: Device group with Intune Provisioning Client as owner*

#### 2. Create a Device Preparation Policy

Devices → Enrollment → Windows → Windows Autopilot device preparation policies → Create.

- **Basics:** name and description
- **Device group:** `SG-Win11-DevicePrep-Devices`
- **Configuration settings:** User-driven, Single user, Microsoft Entra joined, Standard user, timeout and custom error message
- **Assignments:** `SG-Win11-DevicePrep-Users`

<img src="docs/img/02-policy.png" alt="Policy" width="800" />

*Ref 2: Device preparation policy*

#### 3. Select Apps and Scripts

In the policy, select the apps and scripts that OOBE waits for, so the minimum needed set installs before the user reaches the desktop. In Intune, assign the same apps and scripts as **Required** to the **device group**, otherwise they do not install.

<img src="docs/img/03-apps-assigned.png" alt="Apps" width="800" />

*Ref 3: App assignment in device preparation*

#### 4. Enroll a Device With No Pre-Registration

Boot a factory-reset Windows 11 device to OOBE. No hardware hash is imported beforehand. When a user from the assigned group signs in, the device is enrolled and added to the device group (enrollment time grouping). The device must not be registered as a classic Autopilot device, otherwise the Autopilot profile takes precedence.

<img src="docs/img/04-hashless-enrollment.png" alt="Hashless enrollment" width="800" />

*Ref 4: Hashless enrollment*

#### 5. Monitor the Deployment

Devices → Monitor → Windows Autopilot device preparation deployments.

<img src="docs/img/05-monitoring.png" alt="Monitoring" width="800" />

*Ref 5: Deployment report*

#### 6. Comparison: Traditional Autopilot vs. Device Preparation

| Aspect | Traditional Autopilot | Device Preparation |
|---|---|---|
| Pre-registration | Yes, hardware hash imported ahead of time | No, device registers at first sign-in |
| Join type | Entra joined, Hybrid joined | Entra joined only |
| OS support | Windows 10 and 11 | Windows 11 (22H2/23H2 with updates, 24H2) |
| Targeting | Autopilot profile on the device | User group (assignment) plus device group |
| Autopilot Reset | Yes | No |
| Best fit | Planned rollouts, hybrid environments | Unplanned replacements, shared and frontline devices |

*Ref 6: Provisioning model comparison*

## About

Hash-free Autopilot provisioning model, configured and compared with the traditional flow.
