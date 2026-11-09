---
layout: post
title: "Ivanti Patching Isn't Eviction"
date: 2026-11-02T07:40:00-05:00
tags: [dfir, ivanti]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7523000966623694848/
source: https://www.cisa.gov/news-events/alerts/2026/10/13/cisa-urges-patching-newly-exploited-vulnerabilities-ivanti-connect-secure
type: post
---

Patching an edge appliance is not the same as evicting an attacker.

The Ivanti Connect Secure chain that Volexity tracks as UTA0576 illustrates just why that is. Ivanti's Oct 13 fix closed the authentication bypass. The privilege escalation and file-upload flaws in the same chain were fixed later, and CISA added both to KEV on Oct 30.

What stands out from the IR perspective is that the actor patched code in memory, restored the configuration, deleted entries from the debug and audit logs, and left no web shell. Volexity says Ivanti's Integrity Checker doesn't detect that, so a clean ICT result is meaningless.

They also went after LAPS passwords and BitLocker recovery keys through the appliance's AD access. The incident doesn't stay on the VPN.

If you ran an affected version this month:
- Upgrade to a fixed release (25.1.1.3 or 22.8R2.6)
- Scope with network and memory forensics, not file integrity alone (you are capturing netflows, right?)
- If compromise is suspected, follow CISA's recovery steps: factory reset or rebuild, restore the config (you have a backup, right?), reset the domain, admin and local passwords, reissue new certificates and keys
- Review who can read your LAPS and BitLocker data (which you have documented, right?)

Edge devices are often where evidence is thinnest and blast radius is largest. Check out Cisco's reference architecture for placing VPN devices behind control points to enable network logging of VPN traffic: https://www.cisco.com/c/en/us/solutions/collateral/enterprise/design-zone-security/safe-secure-edge-architecture-guide.html

CISA's updated guidance: https://www.cisa.gov/news-events/alerts/2026/10/13/cisa-urges-patching-newly-exploited-vulnerabilities-ivanti-connect-secure

#DFIR #IncidentResponse #ThreatIntel
