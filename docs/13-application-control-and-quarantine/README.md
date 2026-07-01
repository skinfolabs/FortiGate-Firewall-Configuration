# Application Control and Quarantine

This chapter documents Application Control and quarantine response for unauthorized remote-access behavior. It uses TeamViewer as the controlled application example and follows the path from profile creation to block logs and a temporary quarantine entry.


## Technical Context

Application Control identifies traffic by application signatures rather than relying only on IP addresses and ports. This allows FortiGate to recognize TeamViewer as a remote-access application even when it uses common encrypted web protocols that would otherwise resemble ordinary HTTPS traffic.

The initial profile blocks the remote-access application category and records the TeamViewer match in FortiGate logs. The response is then strengthened with a two-day quarantine override so repeated prohibited activity produces a longer restriction visible in the User & Devices dashboard.

> Port-based rules cannot reliably distinguish every modern application because many services share ports such as TCP `443`. Application signatures add behavioral and protocol awareness, while quarantine adds a temporary containment response after the prohibited activity is identified.

**Implemented controls:**

- Created `Office_Application_Control`.
- Blocked the remote-access application category containing TeamViewer.
- Applied the profile to outbound traffic.
- Validated the TeamViewer block with client behavior and logs.
- Configured and triggered a two-day quarantine action.
- Reviewed the resulting quarantine entry in FortiGate.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Application Control | FortiGate profile that identifies applications by signatures and behavior, not only by port number. |
| Remote-access category | Application category containing tools such as TeamViewer that can create unauthorized remote-control paths. |
| Quarantine | A containment action that temporarily restricts a source after prohibited activity is detected. |
| Override | A profile-specific action that changes how a matched application is handled. |

---

## Detailed Walkthrough

### Step 01 - Create the Application Control profile

The `Office_Application_Control` profile is created and the remote-access application category containing TeamViewer is set to block. Category-based control applies the organizational restriction to the related remote-access signatures included in that category instead of relying on one destination hostname.

> Remote-access tools can create an alternate path into or out of an organization that bypasses approved support channels. Blocking the category reduces unauthorized remote control, but legitimate business exceptions should be identified and scoped deliberately.

![Application Control profile](../../images/13-application-control-and-quarantine/01.png)

<p><sub><strong>Screenshot 080 - Office Application Control Profile:</strong> The remote-access application category is selected for blocking.</sub></p>

---

### Step 02 - Apply Application Control to outbound traffic

The new Application Control profile is selected on the outbound firewall policy. This ensures that office sessions allowed toward the internet are also classified by application before FortiGate decides whether the detected tool is permitted.

> Application Control does not operate on traffic that never reaches its attached policy. Correct interface direction, policy order, and SSL inspection capabilities influence whether FortiGate can identify the application reliably.

![Application Control policy](../../images/13-application-control-and-quarantine/02.png)

<p><sub><strong>Screenshot 081 - Application Control Policy Attachment:</strong> The custom Application Control profile is enabled on the outbound rule.</sub></p>

---

### Step 03 - Test TeamViewer access from the workstation

The workstation attempts to reach TeamViewer and receives a browser security failure instead of establishing normal access. This is the client-side indication that the session was interrupted after the Application Control profile was applied.

> The browser message alone could also be produced by a TLS or connectivity problem. The matching Application Control log is therefore the stronger evidence that FortiGate identified TeamViewer and enforced the configured block action.

![TeamViewer client block](../../images/13-application-control-and-quarantine/03.png)

<p><sub><strong>Screenshot 082 - TeamViewer Client Block:</strong> The workstation cannot establish the TeamViewer-related connection after Application Control is enabled.</sub></p>

---

### Step 04 - Confirm the application block in logs

The Application Control logs are reviewed after the workstation test. The entries identify TeamViewer and show the block action, connecting the client failure with the configured application signature and outbound policy.

> Application logs provide visibility that a port-based firewall rule cannot. They show which application FortiGate recognized, what action was taken, and which source generated the controlled event.

![Application Control logs](../../images/13-application-control-and-quarantine/04.png)

<p><sub><strong>Screenshot 083 - TeamViewer Application Logs:</strong> FortiGate identifies TeamViewer traffic and records a block action.</sub></p>

---

### Step 05 - Configure a two-day quarantine action

An Application Control override is added for TeamViewer with a two-day quarantine period. Instead of handling only the current session, FortiGate can create a temporary banned entry after the application match and continue restricting the affected source for the configured duration.

> Quarantine is a containment action and should be scoped carefully. A broad or false-positive quarantine can interrupt legitimate work, so production use requires clear expiry, review, monitoring, and an administrative process for release.

![Application quarantine override](../../images/13-application-control-and-quarantine/05.png)

<p><sub><strong>Screenshot 084 - Two-Day Quarantine Override:</strong> TeamViewer activity is configured to trigger a quarantine lasting two days.</sub></p>

---

### Step 06 - Trigger the quarantine condition

The TeamViewer-related activity is generated again from the workstation after the override is saved. Repeating the same controlled behavior allows FortiGate to match the application and apply the newly configured quarantine response rather than only the original block action.

> Security controls should be retested after the response action changes. A previous block event cannot prove that a later quarantine override is active, because the policy behavior at the time of the first test was different.

![TeamViewer quarantine test](../../images/13-application-control-and-quarantine/06.png)

<p><sub><strong>Screenshot 085 - Quarantine Trigger Test:</strong> The workstation repeats the TeamViewer test after the quarantine override is configured.</sub></p>

---

### Step 07 - Validate the quarantined user

The FortiGate User & Devices dashboard is opened and the resulting banned entry is visible after the repeated TeamViewer test. This screen is the administrative confirmation that the quarantine workflow created a persistent restriction beyond the single blocked browser session.

> The dashboard is also the operational location for reviewing the affected identity or source, expiry, and release action. Quarantine should be investigated rather than treated as a self-explanatory final result, especially when business-critical endpoints are involved.

![FortiGate quarantine dashboard](../../images/13-application-control-and-quarantine/07.png)

<p><sub><strong>Screenshot 086 - Quarantine Dashboard:</strong> FortiGate displays the quarantined user or source entry after the Application Control test.</sub></p>

---

## Validation and Summary

Validation combines the TeamViewer client failure, Application Control logs, quarantine override, repeated trigger test, and the FortiGate quarantine dashboard. This documents both the application block and the longer containment response.

This chapter completes the Application Control and quarantine workflow. The screenshots validate the block decision, log evidence, quarantine override, repeated trigger, and final User & Devices quarantine view.

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
