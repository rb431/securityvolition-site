---
layout: post
title: When Your RMM Vendor Goes Dark
date: 2026-11-13T14:54:01-05:00
tags: [n-able, rmm, third-party-risk, customer-comms]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7527096452813111296/
source: https://www.huntress.com/blog/n-able-disables-cloud-services-following-disclosure-of-able2shell-exploit-chains
type: post
---

N-able took its hosted N-central environment offline on Nov 9 after a researcher published 33 critical vulnerabilities, chainable into four exploits dubbed Able2Shell. On-prem customers were told to take their servers offline too. N-able said it hadn't seen exploitation.

That was the right call, and a really hard one. Now picture it from the MSP side: the tool you use to patch, script, and remote into every client goes dark with no firm ETA, and your clients are asking whether they're exposed.

This is where IR delivery earns trust or loses it. The control is a tested critical-vendor outage runbook for your management plane. What I'd want to see:
- A documented, out-of-band way to reach and patch managed endpoints when the RMM is down
- Pre-written client notices that say what you know, what you don't, and when you'll update next
- A scoping checklist ready before you power back on: logins since the disclosure, new admin accounts, scripts pushed, unexpected remote sessions

That last item matters. In earlier N-central exploitation, attackers pushed scripts and tools downstream into managed environments. One console, many victims.

And it happened again this week. On Nov 11, per Huntress, attackers abused a third-party ScreenConnect extension called RMM+, which accepted a blank access key and skipped login and MFA, to push a toolkit called SCKit to at least 288 endpoints across 48 orgs in about 90 minutes. Your RMM's plugins and extensions belong in that runbook too. Who on your team is tracking those?

Tabletop the day your RMM vendor goes dark. It just happened.

https://www.huntress.com/blog/n-able-disables-cloud-services-following-disclosure-of-able2shell-exploit-chains

#IncidentResponse #MSP #ThirdPartyRisk
