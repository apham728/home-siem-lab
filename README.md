# Home SIEM Lab: Wazuh Detection in VirtualBox

> A virtualized SIEM lab where I deployed Wazuh, collected Windows endpoint telemetry with Sysmon, simulated attacker techniques, and wrote custom detection rules mapped to MITRE ATT&CK.

## Objectives
- Learn about log collection and detection rule writing
- Understand more about log analysis
- Capture logs from Wazuh through Sysmon  

## Architecture

![Lab architecture diagram](screenshots/architecture.png)

| Component | Role | Specs |
|---|---|---|
| Wazuh server VM | Manager, indexer, dashboard | Ubuntu Server 24.04, 4 vCPU, 8 GB RAM, 50 GB disk |
| Windows 11 VM | Monitored endpoint | Windows 11 Enterprise, 2 vCPU, 4 GB RAM, 60 GB disk |
| Network | Isolated VirtualBox NAT Network (`siemlab`) | Dashboard reached via host port forward 8443 -> 443 |

**Design decision:** I used a NAT Network because it isolates my VMs from my home network, using the host computer as a virtual router.  This also gives mores security/safety to my home network from anything I'm working on in my VMs and prevents anything leaking into my home network.

## Tools Used

- VirtualBox 7.2.22
- Ubuntu Server 24.04 ISO
- Windows 11 Enterprise 26H2 ISO 
- Wazuh 4.14.10 (all-in-one deployment)
- Sysmon 15.22 + SwiftOnSecurity Config
- Atomic Red Team

**Design decision:** I used the SwiftOnSecurity Sysmon config rather than Sysmon out of the box as it is well documented and readable. It focuses on logging events worth analyzing (file creation, process creation, DNS queries, etc.) while filtering out noise that would otherwise flood the logs.

## Setup Summary

1. Created the isolated network and VMs
2. Installed Wazuh server
3. Deployed and enrolled the Windows agent
4. Installed Sysmon and forwarded its logs to Wazuh
5. Took snapshots at each stable point

Full steps: [docs/setup.md](docs/setup.md)

## Attack Simulations and Detections
Attack simulations are documented at: [tests/atomic-tests.md](tests/atomic-tests.md)

## Custom Detection Rules
Any custom rules written are in: [rules/custom_rules.md](rules/custom-rules.md)

## Repository Layout

```
.
├── README.md
├── docs/
│   ├── setup.md
├── rules/
│   └── local_rules.xml
├── tests/
│   └── atomic-tests.md
└── screenshots/
```

## Safety Note

All attack simulations were run only inside isolated virtual machines that I own. No credentials or personal data are stored in this repository.
