# Windows Autopilot Device Preparation (New Experience)

## Objective

Set up Windows Autopilot device preparation policies in Intune, a lighter provisioning model for Entra joined Windows devices that works without hardware hashes, and compare it with the traditional Windows Autopilot flow.

### Skills Learned

- Understanding how the traditional Autopilot flow works (hardware hash, deployment profile, Enrollment Status Page)
- Preparing the Entra groups required by device preparation, including the owner of the device group
- Creating a user-driven device preparation policy (settings, apps, scripts, assignment)
- Comparing user experience, limits and admin overhead of both models

### Tools Used

- Microsoft Intune admin center
- Microsoft Entra ID groups

## Background: Traditional Autopilot

Traditional Windows Autopilot needs the following before a device can be deployed:

- **Hardware hash** of every device, imported into the tenant (manually with `Get-WindowsAutopilotInfo -Online`, via CSV, or by the vendor)
- **Deployment profile** for OOBE settings: deployment mode, join type (Entra joined or Hybrid joined), account type, language and region, device naming
- **Enrollment Status Page (ESP)** to show progress, set the timeout and error message, and block the device until required apps are installed

The ESP goes through three phases: device preparation, device setup and account setup. The user can sign in after the last phase finishes.

## Steps

#### 1. Prepare the Entra Groups

Two security groups are needed:

- **Device group** (`SG-DevicePrep-Devices`): Assigned membership, created empty. Intune adds devices to it automatically during enrollment. It must have the **Intune Provisioning Client** service principal as **Owner** (AppId `f1346770-5b25-470b-88bd-d5744ab7952c`). In some tenants it is named *Intune Autopilot ConfidentialClient*. If it is missing in the tenant, add it by following the Microsoft Learn guide for adding the Intune Provisioning Client service principal.
- **User group** (`SG-DevicePrep-Users`): users (vendor accounts) who are allowed to deploy devices with the policy.

<img width="936" height="384" alt="image" src="https://github.com/user-attachments/assets/cff3e4af-5f59-49aa-8317-b13a377dcb0e" />

*Ref 1: Device group with the Intune Provisioning Client as owner*

#### 2. Create the Device Preparation Policy

Devices → Enrollment → Device preparation policies → Create → **User-driven**.

- **Basics:** name and description
- **Device group:** select `SG-DevicePrep-Devices` (it can be empty)

<img width="1232" height="342" alt="image" src="https://github.com/user-attachments/assets/9fecdf9b-8eb4-44af-9039-d2248dfa50cb" />

#### 3. Configuration Settings

- **Deployment settings:** deployment mode, join type (Microsoft Entra joined), user account type (Standard user or Administrator)
- **Out-of-box experience:** minutes before an installation error is shown, custom error message, allow users to skip setup after multiple attempts, show link to diagnostics
- **Apps:** up to 10 apps installed before the user reaches the desktop. Company Portal and Microsoft 365 Apps are a good minimal set, the rest can be installed later from Company Portal.
- **Scripts:** up to 10 scripts that run before the user can sign in

#### 4. Scope Tags and Assignment

Add scope tags if needed, then assign the policy to `SG-DevicePrep-Users`. Review and create.

<img width="1232" height="342" alt="image" src="https://github.com/user-attachments/assets/bf90fe00-cc18-4dcc-91f4-2a16d20003cb" />

*Ref 5: Assignment and review*

#### 5. Enroll a Device With No Pre-Registration

Start the device, go through OOBE (language, network) and sign in as a user from `SG-DevicePrep-Users`. No ESP is shown. Instead a single deployment screen goes through these stages:

1. Installing the Intune Management Extension
2. Installing apps and policies
3. OOBE questions
4. Checking for updates
5. Finalizing the deployment
6. Windows Hello for Business setup (if configured)
7. Desktop

Afterwards the selected apps (Company Portal, Microsoft 365 Apps) are present on the device.

<img width="1026" height="872" alt="Snímka obrazovky 2026-10-04 123738" src="https://github.com/user-attachments/assets/bc22c117-c6eb-43da-88f2-276b27955635" />

*Ref 6: Device preparation deployment screen*

#### 6. Notes and Good to Know

- If a device has its **hardware hash imported**, the traditional Autopilot profile wins and the user gets the traditional experience.
- Device preparation has no hardware hash and works for any Windows device, corporate or personal. Restricting enrollment to corporate devices is possible with device platform restrictions combined with corporate device identifiers.
- Device preparation supports **Entra join only**, hybrid join is not supported.
- Limits: 10 apps and 10 scripts per policy.
- There is also an **Automatic (Preview)** policy type for Windows 365 Frontline Cloud PCs in shared mode. It needs the device group but no user assignment.
- Customization is limited compared to traditional Autopilot (no device naming template, the OOBE questions cannot be skipped).

#### 7. Comparison: Traditional Autopilot vs. Device Preparation

| Aspect | Traditional Autopilot | Device Preparation |
|---|---|---|
| Hardware hash | Required, imported ahead of time | Not used |
| Configuration | Deployment profile + ESP | Single policy |
| Join type | Entra joined, Hybrid joined | Entra joined only |
| Apps and scripts before sign-in | Selectable in ESP | Up to 10 apps and 10 scripts |
| Customization | Naming template, OOBE settings, pre-provisioning | Limited |
| User experience | ESP with three phases | One deployment screen |
| Targeting | Device-based (hash + profile) | User group + device group |

*Ref 7: Provisioning model comparison*
