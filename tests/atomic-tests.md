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
| 1 | System Owner/User Discovery | T1033 | [Yes](../screenshots/atomic-test-1.png) | 92022, 92032, 92052 | No | Test ran multiple discovery commands, 18 commands under 1 second. Rule ID 92022 specifies that user info was gathered from Remote Desktop Service sessions |
| 2 | System Network Configuration Discovery | T1016 | Yes but generic | 92031, 92032, 92036, 92052 | [Yes](https://github.com/apham728/home-siem-lab/blob/main/rules/custom-rules.md#t1016-system-network-configuration-discovery) | Alerts went off for the cmd.exe behavior but the alerts are pretty generic such as "Discovery activity detected" and "Suspicious Windows cmd shell execution". Will make custom rule to better detect this |
| 3 | Create Local Account | T1136.001 | [Yes](../screenshots/atomic-test-3.png) | 92033, 92039, 92040 | No | Alerts detected the creation of multiple local accounts. Rule 92040 identifies that an executable ran a user creation command and 92039 detects an executable ran an account discovery command | 
| 4 | Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder | T1547.001 | [Yes](../screenshots/atomic-test-4.png) | 92041, 92226, 92302 | No | Rule 92041 tells us that a registry key was tampered with, Rule 92226 shows that a new executable has been added to the start up folder, and 92302 tells us that a registry entry will be executed upon the next logon, all signs of someone trying to maintain persistence in the system |
| 5 | Command and Scripting Interpreter: PowerShell | T1059.001 | [Yes](../screenshots/atomic-test-5.png) | 92027, 92057 | No | 92027 and 92057 are both codes that identify the PowerShell process opened another PowerShell instance, indicating possible abnormal activity(s) |
| 6 | Masquerading: Rename Legitimate Utilities | T1036.003 | [Yes](../screenshots/atomic-test-6.png) | 61618, 61625 | No | The linked screenshot shows `image: C:\Windows\Temp\lsass.exe`, meaning `lsass.exe` is running from the Temp folder, which is odd in itself. `originalFileName: Cmd.Exe` is different from `image`, which shows the masquerading. The alert is tagged with T1055 for process injection, except nothing was injected here. T1055 was most likely a loose tag for this alert as `lsass.exe` was run in an odd location (Temp folder). |  
