# Project Notes

## Scope

This repository documents a controlled FortiGate lab. It focuses on configuration workflow, security intent, and validation evidence. It does not include live FortiGate configuration exports or reusable automation scripts.

## Technical Notes

### 1. Role-Based Administration

The administrator profile and restricted IT administrator examples demonstrate least privilege. In production, administrator roles should be reviewed regularly and tied to named accounts, MFA, and centralized logging.

### 2. LDAP Authentication

LDAP integration allows FortiGate to use Active Directory users and groups for VPN access. In production, LDAP connectivity should be protected, monitored, and ideally combined with stronger authentication controls such as MFA.

### 3. SSL VPN Access

SSL VPN Tunnel Mode gives users broader network access than Web Mode. Tunnel access should be restricted by user group, destination, service, and posture requirements where possible.

SSL VPN Web Mode is more focused because access is provided through bookmarks such as RDP. It is useful when users need a specific internal resource rather than full network access.

### 4. Virtual IP Publishing

The Virtual IP example publishes an internal IIS service through FortiGate. In production, public service publishing should be limited by source, service, logging, TLS settings, IPS, WAF or reverse proxy controls where appropriate, and patching of the internal service.

### 5. Site-to-Site IPsec VPN

The IPsec VPN section intentionally restricts traffic by service. A VPN should not automatically mean full trust between sites. Directional policies and specific allowed services reduce the attack surface between connected networks.

### 6. SSL/TLS Inspection

Full SSL/TLS inspection can improve detection for encrypted traffic, but it requires trusted certificate deployment, privacy review, exception handling, and application compatibility testing.

### 7. Web and DNS Filtering

The Web Filter and DNS Filter examples demonstrate user-facing enforcement and log validation. Both controls should be tested from a client endpoint because policy configuration alone does not prove that users are actually affected.

### 8. Antivirus and IPS Validation

The antivirus and IPS sections use client behavior and FortiGate logs to validate enforcement. This is important because a security profile is only useful when it is attached to the correct policy and produces observable results.

### 9. Application Control and Quarantine

Application Control is used to block remote-access tools such as TeamViewer and demonstrate user quarantine. In production, quarantine actions should be scoped carefully to avoid disrupting legitimate support workflows.

## Files Intentionally Not Included

No separate command or script files were added because this project is based on GUI-based FortiGate configuration and screenshot validation. If future versions include CLI configuration, automation, or exported firewall rules, those should be stored under `configs/`, `scripts/`, or `docs/commands.md`.

