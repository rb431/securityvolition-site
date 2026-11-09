---
layout: post
title: The Help Desk Call Is the Breach
date: 2026-11-09T13:10:00-05:00
tags: [leadership, customer-facing, social-engineering, ransomware]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7525620728965079041/
source: https://blog.talosintelligence.com/ir-trends-q3-2026/
type: post
---

Cisco Talos's Q3 2026 IR trends report has a finding every service desk leader should read: attackers are calling users while impersonating the IT help desk, then talking them into opening a remote session with a legitimate tool like Microsoft Quick Assist. In one engagement, that call was the start of a ransomware intrusion. We also see calls like this regularly leading to BEC's. We have encountered one specialized group that focuses on hotel property management systems as well. They call the front desk at night and convince the night shift to allow remote access. Then they move on to fraudulent charge-backs to a credit card the attacker controls. It ain't millions, but there are a LOT of hotels.

Phishing still led initial access, at 37% of Talos engagements, and ransomware and pre-ransomware rose to 27%. What stands out is that the weak point wasn't a missing tool. It was a process that let a voice on the phone stand in for identity.

Identity must be verified in both directions. Users need a known way to confirm a call really is IT, such as a callback through the published number or a ticket they can see. The help desk needs out-of-band verification before any MFA reset, re-enrollment, or remote session, with extra checks for privileged accounts. Have a scripted verification step nobody skips, and remote-access tools that are allow-listed so a new one launching raises an alert. You would see that alert, right?

In post-incident briefings, the fix that sticks is usually a process change, not a new product.

#IncidentResponse #SocialEngineering #Ransomware

https://blog.talosintelligence.com/ir-trends-q3-2026/
