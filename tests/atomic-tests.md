# Atomic Red Team Tests
Attack simulations I ran in the lab and what was logged by Sysmon and alerted by Wazuh is documented here.

## Before Attack Simulations
- Delete previously made test files (such as test.exe on Desktop)
- Defender exclusion added for the Atomic folder in the VM:
  ```powershell
  Add-MpPreference -ExclusionPath "C:\AtomicRedTeam"
  ```
- Install Atomic Red Team
  ```powershell
  Set-ExecutionPolicy Bypass -Scope Process -Force
  IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
  Install-AtomicRedTeam -getAtomics
  ```
- Disable shared clipboard (Devices > Shared Clipboard > Disabled) for isolation purposes + general precaution

## Results
| # | Technique | ATT&CK ID | Detected by default? | Rule ID(s) | Need custom rule? | Notes |
|---|---|---|---|---|---|---|
| 1 | System Owner/User Discovery | T1033 | Yes | 92022, 92032, 92052 | No | Test 1 ran multiple discovery commands, 18 commands under 1 second. Rule ID 92022 specifies that user info was gathered from Remote Desktop Service sessions |
| 2 | System Network Configuration Discovery | T1016 | Yes but generic | 92031, 92032, 92036, 92052 | [Yes](https://github.com/apham728/home-siem-lab/blob/main/rules/custom-rules.md#t1016-system-network-configuration-discovery) | Alerts went off for the cmd.exe behavior but the alerts are pretty generic such as "Discovery activity detected" and "Suspicious Windows cmd shell execution". Will make custom rule to better detect this |
