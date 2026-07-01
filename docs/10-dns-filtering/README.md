# DNS Filtering

This chapter documents DNS Filtering for controlled domain blocking and DNS-filter log validation. It focuses on decisions made at the name-resolution layer before a browser completes normal access to a domain.


## Technical Context

DNS filtering evaluates domain-resolution requests before the client establishes the full application connection. FortiGate can compare queried names with FortiGuard threat intelligence, block domains associated with botnet or command-and-control activity, and apply organization-defined static rules for controlled destinations.

This exercise enables C&C protection and then uses Facebook as a safe demonstration domain. The static rule redirects the test request to a FortiGate block portal, and both the client result and DNS-filter log are retained to show enforcement.

> DNS filtering acts early in the connection process by preventing or changing name resolution. It does not inspect every later packet to the destination, so layered controls such as firewall policy, Web Filter, IPS, and Application Control still remain important.

**Implemented controls:**

- Created `Office_DNS_Filter`.
- Enabled known botnet and C&C domain protection.
- Added a controlled wildcard domain rule.
- Applied the DNS filter to outbound traffic.
- Validated the client warning and corresponding DNS logs.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| DNS Filter | FortiGate profile that evaluates domain lookups before normal site access completes. |
| C2 | Command and Control infrastructure used by malware to receive instructions or exfiltrate data. |
| Static domain block | A manually configured domain pattern used for controlled filtering tests. |
| DNS log | Firewall evidence showing the queried domain, source, and action. |

---

## Detailed Walkthrough

### Step 01 - Enable botnet and C&C protection

The `Office_DNS_Filter` profile enables FortiGuard-based blocking for known botnet and command-and-control destinations. If an infected system tries to resolve a listed control domain, FortiGate can block or redirect the request before the host reaches the attacker's server.

> Command-and-control infrastructure is used to issue instructions, receive stolen data, or update malware. DNS blocking is valuable because malicious infrastructure often changes IP addresses while continuing to rely on recognizable domain names.

![DNS filter profile](../../images/10-dns-filter/01.png)

<p><sub><strong>Screenshot 067 - Office DNS Filter Profile:</strong> Botnet and C&C domain protection is enabled in the DNS filter profile.</sub></p>

---

### Step 02 - Create a controlled domain-blocking example

Facebook is added as a wildcard static domain-filter example and assigned the redirect-to-block-portal action. The familiar domain makes the test easy to recognize and demonstrates how a local administrator-defined rule can override normal resolution behavior.

> Facebook is not being classified as genuinely malicious in this exercise. It is a safe demonstration target used to prove the mechanics of a custom DNS rule without directing the workstation toward real command-and-control infrastructure.

![Static DNS domain rule](../../images/10-dns-filter/02.png)

<p><sub><strong>Screenshot 068 - Static Domain Filter Entry:</strong> A wildcard Facebook domain rule is configured with a redirect-to-block-portal action.</sub></p>

---

### Step 03 - Apply the DNS filter to outbound traffic

`Office_DNS_Filter` is enabled on the outbound firewall policy used by the workstation. This connects the DNS inspection logic to matching client traffic so subsequent domain queries can be evaluated by the profile.

> Creating a DNS filter does not affect traffic until the correct policy references it. The policy path must also carry the client's DNS requests; otherwise the profile can be configured correctly and still produce no result.

![DNS filter applied](../../images/10-dns-filter/03.png)

<p><sub><strong>Screenshot 069 - DNS Filter Policy Attachment:</strong> `Office_DNS_Filter` is enabled on the outbound firewall policy.</sub></p>

---

### Step 04 - Validate the client block result

The workstation attempts to open the controlled domain and receives the configured FortiGate warning instead of the normal website. This shows that the DNS decision changed the user's connection path and redirected the request to the block portal.

> A client warning demonstrates the user experience, but the administrator should still verify the DNS log. That log confirms the queried name and action and helps distinguish DNS filtering from browser, routing, or web-filter issues.

![DNS filter client warning](../../images/10-dns-filter/04.png)

<p><sub><strong>Screenshot 070 - DNS Filter Client Result:</strong> The browser displays a blocked-page warning for the controlled domain-filter test.</sub></p>

---

### Step 05 - Review DNS-filter logs

The DNS Filter log is reviewed to confirm that FortiGate processed the workstation's domain request and applied the configured block or redirect action. The source, queried domain, and result provide the administrative explanation for the client warning.

> Client evidence and firewall evidence answer different questions. The warning shows what the user experienced, while the log shows which FortiGate control produced that experience and records it for troubleshooting or review.

![DNS filter logs](../../images/10-dns-filter/05.png)

<p><sub><strong>Screenshot 071 - DNS Filter Logs:</strong> FortiGate records the blocked domain request generated by the workstation.</sub></p>

---

## Validation and Summary

Validation combines a controlled client block result with FortiGate DNS-filter logs. The log view is the stronger administrative evidence because it identifies the queried domain and FortiGate action.

This chapter completes the DNS-filtering workflow. The client warning and DNS-filter logs together show that FortiGate processed the domain lookup and applied the configured control.

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
