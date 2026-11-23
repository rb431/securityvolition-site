---
layout: post
title: Shorter Disclosure Clocks
date: 2026-11-23T16:35:01-05:00
tags: [dfir, vulnerability-management, leadership, ai-security]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7530745748930576385/
source: https://www.bleepingcomputer.com/news/security/google-project-zero-cuts-bug-disclosure-deadline-to-30-days-amid-ai-threats/
type: post
---

Two vulnerability disclosure clocks got shorter last week, and I don't think most patch programs are built for it.

Google Project Zero dropped its extra 30-day grace period on Nov 13. Its deadline goes to 60 days in February 2027 and 30 days in January 2028, and details publish whether a fix exists or not. Anthropic says that starting Jan 1, 2027, it will publish high and critical bugs it reports after 30 days, down from 90. Same reason from both: AI is speeding up how fast bugs get found and how fast patches get turned into working exploits.

That second part matters most. Project Zero now calls zero patch-to-exploitation time "a reasonable, though imperfect, model of attacker behavior." Anthropic tested it in June. Its strongest model had a working Firefox exploit within an hour of Mozilla landing the fix, 18 days before the patched browser even shipped. On Windows kernel patches, without source code, it built 8 full SYSTEM exploit chains from 21 fixes.

Attackers are already on it. Google's threat intel team found an attacker framework called exploit_agent that diffs patched and unpatched versions of popular web apps, then writes and runs exploits with no human in the loop.

The manual way still works too. Citrix called a June NetScaler fix a DoS bug. watchTowr diffed the patched binary, found pre-auth RCE as root, and attackers were dropping webshells within days of the write-up.

So the hotfix itself is the blueprint. Your clock starts the day the vendor ships it. Remember, the vulnerability is ALREADY there in your systems, and it's just waiting for the attacker.

For managers, the short version: technical details on serious flaws will be public sooner, and attackers read them too.

A monthly patch cycle with a 30-day SLA on criticals made sense for a long time. Does it still? How about when a working exploit can exist before your next CAB meets?

Done right, that's:
- an emergency change lane leadership approves ahead of time, so a critical on an internet-facing system ships within days of the vendor's release without waiting for the next CAB
- an asset inventory that can answer "do we even run this?" within the hour
- a playbook of compensating controls (isolate it, turn the feature off) for the gap between patch release and your deploy, or when there's no patch yet
- a compromise check when exploitation was public before you patched, because patching doesn't evict anyone

I'd rather have this conversation with leadership now, while it's a policy discussion.

https://www.bleepingcomputer.com/news/security/google-project-zero-cuts-bug-disclosure-deadline-to-30-days-amid-ai-threats/

#VulnerabilityManagement #CyberLeadership #IncidentResponse
