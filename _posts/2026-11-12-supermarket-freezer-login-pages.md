---
layout: post
title: Supermarket Freezer Login Pages
date: 2026-11-12T08:17:01-05:00
tags: [dfir, ot-security, third-party-risk, incident-response]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7526634157691572224/
source: https://x.com/lukedewolf/status/2117635330710941996
type: post
---

About 700 French supermarkets lost their freezers to an attacker last month, and nobody needed a zero-day.

Luke de Wolf's write-up of the refrigeration attacks is worth your time. Starting the night of Oct 11-12, attackers changed settings on internet-exposed refrigeration controllers. CERT-FR named Danfoss, Copeland and Dixell. A second wave hit other European countries overnight into Nov 2. Attribution is still unknown.

ANSSI's director told the French Senate the way in was internet exposure, unprotected admin access and weak or default passwords, not the published CVEs.

Why were they online? Mostly so the refrigeration contractor could monitor and manage them remotely. That's the part every IR team should take home. Your OT exposure is often your vendor's remote access. (shakes head and sighs)

Two data points stand out:
- Censys counted 1,825 exposed Danfoss StoreView interfaces in France before the attack and 220 by Nov 2. Operators can fix this fast once they have a reason to.
- In Estonia, Selver's refrigeration partner responded within 10 minutes. Temperatures rose only 2-3 degrees and no product was lost. Monitoring plus a partner who answers works.

These controllers aren't only a European problem. Censys says the US has the third-most exposed StoreView interfaces, behind France and Belgium.

What I'd do this week:
1. Inventory building and OT management interfaces reachable from the internet. Check HVAC, refrigeration and access control, not just servers.
2. For each one, name the contractor who uses it and how. Move that access behind a VPN or protected network. Change default passwords.
3. Put vendor response times in the contract and test them. Selver's 10 minutes didn't happen by accident.
4. Add OT to your IR playbooks. Who can pull a controller offline at 3 AM? Does cutting the contractor's data feed affect your insurance? CERT-FR says to check before you cut it.
5. After an incident, review the whole configuration, including defrost schedules. Replace end-of-life units. A factory reset alone leaves them vulnerable.

If an attacker can reach a login page, assume they will try it.

https://x.com/lukedewolf/status/2117635330710941996

#DFIR #IncidentResponse #OTSecurity
