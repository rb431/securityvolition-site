---
layout: post
title: Fast Inference AI Intrusions
date: 2026-11-10T10:23:01-05:00
tags: [dfir, ai-security, incident-response, containment]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7525941089816657922/
source: https://x.com/benharelone/status/2117651795610939555
type: post
---

If your containment step needs a human to approve it, an AI-run intrusion will probably be finished before anyone does.

Ben at MOXFIVE published a sharp piece on this. A few points from it that every IR lead should read:

- Machine-speed attacker activity went from about one in six of their investigations in Q4 2025 to more than six in ten by Q3 2026.
- Attackers are now running many fronts at once: pulling data, minting credentials, creating accounts and changing permissions in the same minutes.
- Fast inference (1,000+ tokens per second) is already for sale. When they re-timed real cloud intrusions at that speed, one run from a hijacked developer account to production secrets dropped from about 11 minutes to as little as 2.
- Cloud audit logs often take around five minutes just to arrive. Five to fifteen minutes from action to alert is common.

Do the math: the first alert can describe something that already happened.

What I'd do now, before that day arrives:

1. Pre-authorize containment. Decide in writing which actions run without sign-off once an attack is confirmed: disable the identity, revoke sessions and tokens, isolate the endpoint, lock down the cloud role. Get legal and the business to agree now, not at 2 AM.
2. Measure your pipeline, not just your analysts. Time action to log, log to alert, alert to containment. Most teams only track the last one.
3. Prove you can contain in the cloud control plane, not only on endpoints. Test it.
4. Shrink what's exposed. A faster attacker gets more from every reachable system. Harel's advice is to put an identity-aware edge in front of public apps.
5. Plan scoping for parallel workstreams. Thousands of interleaved requests from many addresses won't untangle by hand.

The decisions that matter have to be made before the incident, not during it.

https://x.com/benharelone/status/2117651795610939555

#DFIR #IncidentResponse #AISecurity
