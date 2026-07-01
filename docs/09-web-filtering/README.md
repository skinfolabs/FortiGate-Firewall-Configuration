# Web Filtering

This chapter documents FortiGate Web Filtering with static URL blocks, category-based authentication, client testing, and log review. It shows how a profile becomes meaningful only after it is attached to the active outbound policy and validated from a workstation.


## Technical Context

Web filtering controls browser access according to FortiGuard categories, explicit URL patterns, and authenticated user identity. The `Office_Web_Filter` profile is configured in proxy mode, allowing FortiGate to process the web request as an intermediary and apply more detailed actions before the connection is completed.

The lab objective includes the Job Search category, a wildcard block for Reddit, and authenticated access for the Shopping category. The screenshots directly demonstrate creation of the proxy profile, the explicit Reddit rule, the Shopping authentication action, policy attachment, client behavior, and FortiGate logging.

> A web filter profile defines the inspection logic, while the firewall policy decides which traffic receives that profile. URL rules and categories can overlap, so validation must show the resulting action and logs rather than assuming the intended entry was the one that matched.

**Implemented controls:**

- Created the proxy-based `Office_Web_Filter` profile.
- Added a wildcard block for Reddit and its subdomains.
- Required authentication for the Shopping category.
- Selected the group authorized for the category override.
- Applied the profile to outbound traffic.
- Validated client behavior and reviewed web-filter logs.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Web Filter | FortiGate profile that controls web access by URL, category, and user identity. |
| Static URL filter | A manually defined URL or wildcard entry with a specific action. |
| Category authentication | A web-filter action that asks the user to authenticate before category access is allowed. |
| Proxy mode | Inspection mode where FortiGate handles the web request as an intermediary for deeper control. |

---

## Detailed Walkthrough

### Step 01 - Create the proxy-based web filter profile

The Web Filter page is opened and `Office_Web_Filter` is created with proxy-based feature settings. This profile becomes the container for category actions, explicit URL rules, identity-based overrides, and the logging behavior applied later to office web traffic.

> Proxy-based inspection gives FortiGate more control over the application exchange because the firewall terminates and reconstructs the proxied session for policy processing. It can provide deeper control than flow-based handling, with additional resource and compatibility considerations.

![Proxy web filter profile](../../images/09-web-filter/01.png)

<p><sub><strong>Screenshot 058 - Office Web Filter Profile:</strong> `Office_Web_Filter` is created with proxy-based feature settings.</sub></p>

---

### Step 02 - Open the static URL filter

The Static URL Filter area is opened and enabled so the administrator can define an explicit hostname pattern independently of the FortiGuard category assigned to the site. This prepares the profile for the controlled Reddit block.

> Category filtering groups many sites under a shared classification, while a static URL rule targets a specific domain or pattern. Explicit rules are useful for organization-specific exceptions that cannot be expressed by category alone.

![Static URL filter](../../images/09-web-filter/02.png)

<p><sub><strong>Screenshot 059 - Static URL Filter Configuration:</strong> The profile's static URL filtering area is opened for a custom block rule.</sub></p>

---

### Step 03 - Block Reddit and its subdomains

The wildcard expression covers `reddit.com` and its subdomains and is assigned a block action. This prevents the test from depending on one exact hostname and makes the rule apply when the browser reaches alternate Reddit hostnames during navigation.

> A domain can use several subdomains for content, authentication, or supporting services. Blocking only one exact record may leave another hostname reachable, so the wildcard must be designed carefully to cover the intended scope without blocking unrelated domains.

![Reddit wildcard rule](../../images/09-web-filter/03.png)

<p><sub><strong>Screenshot 060 - Reddit Wildcard Block:</strong> The Reddit domain pattern is added with a block action.</sub></p>

---

### Step 04 - Require authentication for Shopping sites

The Shopping category action is changed from a general category decision to authentication. When traffic matches this category, FortiGate can ask the user to identify themselves and then decide whether the authenticated identity belongs to the group allowed to use the exception.

> Authentication changes the question from "Is this category allowed?" to "Which approved user is requesting it?" This supports identity-aware policy without opening the Shopping category to every workstation user.

![Shopping category authentication](../../images/09-web-filter/04.png)

<p><sub><strong>Screenshot 061 - Shopping Category Authentication:</strong> Shopping traffic is configured to require authentication rather than being universally allowed.</sub></p>

---

### Step 05 - Select the authorized user group

The approved user group is selected for the Shopping category override. FortiGate uses this group after authentication to distinguish users who may continue from users who should remain blocked by the general policy.

> The override depends on reliable identity mapping. If authentication is unavailable or the user is not a member of the selected group, FortiGate cannot safely apply the exception and should not treat the request as authorized.

![Shopping authorization group](../../images/09-web-filter/05.png)

<p><sub><strong>Screenshot 062 - Shopping Access Group:</strong> The approved identity group is selected for authenticated Shopping access.</sub></p>

---

### Step 06 - Attach the web filter to the outbound policy

`Office_Web_Filter` is attached to the office-to-internet firewall rule. Once this policy is saved, matching outbound web sessions are evaluated by the profile's category, static URL, and authentication settings instead of passing with only the base firewall decision.

> Security profiles are passive configuration objects until a firewall policy calls them. Attaching the wrong profile or editing a policy that does not match the client traffic would produce no visible filtering result.

![Web filter policy attachment](../../images/09-web-filter/06.png)

<p><sub><strong>Screenshot 063 - Web Filter Policy Attachment:</strong> The new web filter profile is enabled on outbound traffic.</sub></p>

---

### Step 07 - Validate the Reddit block

The workstation attempts to reach Reddit and cannot complete normal access after the wildcard rule is applied. This is the client-side result expected from the explicit block, but the page alone does not identify which network control caused the failure.

> Browser error and block pages are useful user-facing evidence, but similar symptoms can be produced by DNS failure, TLS problems, or an upstream restriction. The matching FortiGate log is needed to attribute the result to the web-filter action.

![Reddit access blocked](../../images/09-web-filter/07.png)

<p><sub><strong>Screenshot 064 - Reddit Client Block Result:</strong> The workstation is unable to open Reddit after the wildcard rule is applied.</sub></p>

---

### Step 08 - Validate authenticated category access

FortiGate presents its authentication page when the workstation requests a site in the controlled Shopping category. This confirms that the category action reached the identity-check stage rather than being treated as a universal allow or block.

> The prompt is an intermediate result. Complete authorization would additionally require valid credentials, membership in the approved group, and a successful continuation to the requested site.

![Web filter authentication prompt](../../images/09-web-filter/08.png)

<p><sub><strong>Screenshot 065 - Web Filter Authentication Prompt:</strong> The client is asked to authenticate before receiving the configured category override.</sub></p>

---

### Step 09 - Review web-filter logs

The Web Filter logs are reviewed after the client tests. The entries show the requested destinations and filtering actions, providing the firewall-side record that connects the browser behavior to `Office_Web_Filter` and the outbound policy.

> Logs turn a visible symptom into an auditable security event. They help an administrator identify the matching policy, category or URL decision, source, destination, user context, and time of enforcement.

![Web filter logs](../../images/09-web-filter/09.png)

<p><sub><strong>Screenshot 066 - Web Filter Logs:</strong> FortiGate records the tested web requests and their filtering actions.</sub></p>

---

## Validation and Summary

Validation combines configuration, workstation behavior, authentication prompt, and FortiGate logs. This confirms that the policy is applied to real client traffic rather than only existing as an unused profile.

This chapter completes the web-filtering workflow. The screenshots connect the configured profile to user-facing browser behavior and FortiGate logs, which makes the enforcement result traceable.

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
