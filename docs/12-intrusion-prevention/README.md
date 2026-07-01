# Intrusion Prevention

This chapter documents Intrusion Prevention using an IPS sensor, controlled test traffic, and dropped-event validation. It distinguishes a generic connection failure from a FortiGate decision backed by an IPS log entry.


## Technical Context

An Intrusion Prevention System examines network traffic for known exploit patterns, suspicious protocol behavior, and botnet indicators. Unlike passive detection, an IPS is placed inline and can drop a matching session before the traffic reaches its destination.

The `Office_IPS_Profile` sensor is attached to the outbound policy and tested from the workstation. The client timeout is then correlated with a FortiGate log entry showing the detected `Bloody.Gang` signature and drop action.

> IPS decisions are based on traffic signatures and protocol context, not simply on whether a website is considered undesirable. A timeout is only a symptom; the IPS log is required to prove that FortiGate identified and blocked the controlled attack pattern.

**Implemented controls:**

- Created `Office_IPS_Profile`.
- Enabled botnet and C&C-related protection.
- Applied the IPS sensor to outbound traffic.
- Generated a controlled client-side test.
- Confirmed the signature and drop action in IPS logs.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| IPS | Intrusion Prevention System, an inline control that can drop traffic matching attack signatures. |
| Signature | A detection pattern for known exploit behavior, malware traffic, or suspicious protocol use. |
| Botnet/C2 protection | Controls intended to identify traffic associated with infected hosts or command infrastructure. |
| Drop action | The firewall response that interrupts a matching session instead of only logging it. |

---

## Detailed Walkthrough

### Step 01 - Create the IPS sensor

The `Office_IPS_Profile` sensor is created under Security Profiles and botnet C&C blocking is enabled. The sensor becomes a reusable inspection object containing the signature and action logic that can be attached to one or more traffic policies.

> An IPS sensor defines which detections FortiGate evaluates and how matches are handled. It should be tuned for the protected applications and monitored for false positives rather than applied as an unexplained universal block list.

![IPS sensor](../../images/12-ips-profile/01.png)

<p><sub><strong>Screenshot 076 - Office IPS Sensor:</strong> The custom IPS profile is created with botnet command-and-control blocking enabled.</sub></p>

---

### Step 02 - Apply IPS to the outbound policy

`Office_IPS_Profile` is enabled on the outbound firewall rule used by the workstation. Matching sessions are now passed through the IPS engine after the base policy allows the traffic.

> Firewall acceptance and IPS inspection are two different decisions. The policy first permits the network flow; the IPS can then interrupt that otherwise allowed flow when the content matches a configured attack signature.

![IPS policy attachment](../../images/12-ips-profile/02.png)

<p><sub><strong>Screenshot 077 - IPS Policy Attachment:</strong> `Office_IPS_Profile` is enabled on the outbound traffic rule.</sub></p>

---

### Step 03 - Generate and observe a controlled IPS test

The workstation attempts to reach the controlled test destination and the browser connection times out. This is the expected user-side effect when the IPS drops the matching traffic before the remote response can complete.

> A timeout alone can be caused by routing, DNS, server availability, or another security control. It becomes meaningful IPS evidence only when the FortiGate log records the same source, destination, time, signature, and drop action.

![IPS client timeout](../../images/12-ips-profile/03.png)

<p><sub><strong>Screenshot 078 - IPS Client-Side Timeout:</strong> The test connection does not complete after the IPS profile is applied.</sub></p>

---

### Step 04 - Confirm the IPS drop in logs

The IPS log records the `Bloody.Gang` signature and a dropped action for the controlled test traffic. This links the workstation timeout to a specific FortiGate detection and confirms that the sensor, policy attachment, and inline enforcement path were active.

> The log supplies the security explanation missing from the browser screen. It also provides the event detail an administrator would use for triage, tuning, and investigation in a production monitoring workflow.

![IPS logs](../../images/12-ips-profile/04.png)

<p><sub><strong>Screenshot 079 - IPS Detection Logs:</strong> FortiGate records and drops the controlled traffic that matched the IPS signature.</sub></p>

---

## Validation and Summary

Validation links the client timeout to a FortiGate IPS log entry showing the detected signature and drop action. This distinguishes IPS enforcement from a generic connectivity failure.

This chapter completes the IPS workflow. The evidence links the client timeout to a FortiGate IPS signature and drop action, which is the key proof that the prevention control handled the test traffic.

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
