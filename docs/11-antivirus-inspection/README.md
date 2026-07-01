# Antivirus Inspection

This chapter documents flow-based FortiGate Antivirus inspection and safe test-sample validation. It demonstrates malware-prevention testing without introducing live malware into the lab environment.


## Technical Context

FortiGate Antivirus scans supported application traffic for files and content that match known malicious signatures or other configured detection methods. The profile is placed on outbound office traffic so a workstation download can be inspected before the file reaches the endpoint.

The lab uses safe vendor-provided WildFire test samples rather than live malware. These files are designed to trigger security products without introducing a real malicious payload, allowing the block page and FortiGate antivirus log to be validated safely.

> Signature-based antivirus identifies known content patterns, while sandbox analysis executes suspicious files in an isolated analysis environment. Sandbox analysis requires the appropriate FortiSandbox or cloud integration; the standard profile shown here does not prove that a sandbox service was enabled.

**Implemented controls:**

- Created the flow-based `Office_AV_Profile`.
- Enabled scanning for the supported application protocols.
- Applied the antivirus profile to the outbound policy.
- Tested the policy with a safe vendor security sample.
- Confirmed the block action in FortiGate antivirus logs.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Antivirus profile | FortiGate security profile that scans supported traffic for known malicious files or signatures. |
| Flow-based inspection | Inspection performed as traffic passes through the firewall instead of fully proxying the session. |
| Test sample | A safe vendor-provided file designed to trigger detection without using live malware. |
| Security log | The firewall-side event record that explains why the client download was blocked. |

---

## Detailed Walkthrough

### Step 01 - Create the flow-based antivirus profile

The `Office_AV_Profile` profile is set to flow-based inspection and scanning is enabled for the listed web, mail, and file-transfer protocols. FortiGate can therefore examine supported content while the session passes through the firewall and block a matching file before the transfer completes.

> Flow-based inspection processes traffic as it moves through the session instead of fully proxying the exchange. The profile still depends on correct protocol support, inspection mode, licensing, and policy attachment to produce an antivirus decision.

![Antivirus profile](../../images/11-antivirus-profile/01.png)

<p><sub><strong>Screenshot 072 - Office Antivirus Profile:</strong> Flow-based antivirus scanning and blocking are enabled for the supported protocols.</sub></p>

---

### Step 02 - Apply antivirus to outbound traffic

`Office_AV_Profile` is selected on the outbound office policy. This places antivirus inspection in the workstation's internet path so supported downloads matching that rule are scanned by the new profile.

> An antivirus profile that is not attached to the active traffic policy cannot protect the client. Policy order and interface direction must be checked so the workstation session actually traverses the rule containing the profile.

![Antivirus policy attachment](../../images/11-antivirus-profile/02.png)

<p><sub><strong>Screenshot 073 - Antivirus Policy Attachment:</strong> `Office_AV_Profile` is selected on the outbound firewall policy.</sub></p>

---

### Step 03 - Test with a safe security sample

The workstation requests a safe WildFire test sample and receives a high-security alert instead of the file. This shows the endpoint-side effect of inline inspection: FortiGate interrupts the download before the sample is delivered normally to the browser.

> A security test sample is preferable to live malware in a training environment. It provides a repeatable detection event while avoiding unnecessary risk to the workstation, hypervisor, or surrounding network.

![Antivirus test block](../../images/11-antivirus-profile/03.png)

<p><sub><strong>Screenshot 074 - Antivirus Test-Sample Block:</strong> FortiGate prevents the workstation from downloading the safe vendor test sample.</sub></p>

---

### Step 04 - Confirm the antivirus event in logs

The Antivirus log is opened after the client test and the blocked sample events are reviewed. The entries provide the file, source, destination, profile, and action context needed to attribute the browser alert to FortiGate antivirus enforcement.

> The client alert shows that the transfer failed, while the antivirus log explains why it failed. Matching these two views creates stronger evidence than relying on a generic browser message alone.

![Antivirus logs](../../images/11-antivirus-profile/04.png)

<p><sub><strong>Screenshot 075 - Antivirus Security Logs:</strong> FortiGate records the blocked test downloads and the antivirus action that handled them.</sub></p>

---

## Validation and Summary

Validation uses a safe vendor test sample and FortiGate antivirus logs. This confirms the antivirus profile is attached to the active policy and can interrupt supported downloads without introducing live malware into the lab.

This chapter completes antivirus inspection validation. The workstation block page and FortiGate antivirus logs confirm that the profile was attached to the active outbound path and responded to the safe test sample.

---

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 0 | [Project Overview](../../README.md) | Main project overview, objectives, tools, and skills |
| 1 | [Topology and Lab Environment](../01-topology-and-lab-environment/README.md) | Topology, lab roles, addressing, trust boundaries, and traffic flow |
| 2 | [Administrator Access and Role-Based Control](../02-administrator-access-and-rbac/README.md) | Named administrator accounts, custom admin profiles, and least-privilege validation |
| 3 | [FortiGate Password Policy Hardening](../03-password-policy-hardening/README.md) | Local password complexity controls for administrators and IPsec pre-shared keys |
| 4 | [LDAP Integration and SSL VPN Tunnel Mode](../04-ldap-and-ssl-vpn-tunnel-mode/README.md) | Active Directory LDAP integration, SSL VPN Tunnel Mode, FortiClient access, and restricted RDP validation |
| 5 | [SSL VPN Web Mode](../05-ssl-vpn-web-mode/README.md) | Browser-based SSL VPN access with LDAP group mapping and an RDP bookmark |
| 6 | [IIS Publishing with Destination NAT](../06-iis-publishing-with-destination-nat/README.md) | Internal IIS publishing through a FortiGate Virtual IP and inbound firewall policy |
| 7 | [Site-to-Site IPsec VPN](../07-site-to-site-ipsec-vpn/README.md) | FortiGate-to-FortiGate IPsec connectivity with directional service restrictions |
| 8 | [Full SSL/TLS Inspection](../08-full-ssl-tls-inspection/README.md) | Full SSL/TLS inspection profile creation and outbound policy attachment |
| 9 | [Web Filtering](../09-web-filtering/README.md) | Static URL filtering, category authentication, client testing, and FortiGate web-filter logs |
| 10 | [DNS Filtering](../10-dns-filtering/README.md) | DNS filter profile configuration, controlled domain blocking, and DNS-filter log review |
| 11 | [Antivirus Inspection](../11-antivirus-inspection/README.md) | Flow-based antivirus profile deployment and safe test-sample validation |
| 12 | [Intrusion Prevention](../12-intrusion-prevention/README.md) | IPS sensor deployment, controlled test traffic, and dropped-event validation |
| 13 | [Application Control and Quarantine](../13-application-control-and-quarantine/README.md) | Application signature blocking, TeamViewer validation, and a temporary quarantine workflow |
| 14 | [Final Summary](../14-final-summary/README.md) | Validation summary, production recommendations, skills, and project closure |
