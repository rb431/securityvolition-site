---
layout: post
title: Alpine GitLab IR Write-up
date: 2026-11-04T12:15:00-05:00
tags: [dfir, gitlab, leadership]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7523795009452740608/
source: https://www.alpinelinux.org/posts/GitLab-security-incident-CVE-2026-97229.html
type: post
---

One of the best incident write-ups I've seen this month came from Alpine Linux, after an attempted exploit of their GitLab server (CVE-2026-97229).

What they got right, from someone who delivers IR findings to customers:

1. They said the hard thing plainly: "We have found no evidence that it succeeded, but we cannot rule it out, so we treated the instance as compromised and rebuilt it from scratch."

2. They separated what they know from what they can't see due to logging gaps. Packages and releases were checked against records the attacker couldn't reach. They also named the gaps: some logs from the attack day are gone, and outbound connections weren't logged.

3. They explained why their own upgrade didn't close the hole. GitLab's fix ships as a patch to the Ruby bundled in GitLab's packages, and their image used standard Ruby.

That's the bar I hold for customer briefings. State the confidence level, show the evidence behind it, say what you can't see, and make the decision the uncertainty calls for. Customers can handle "we can't rule it out." Let privacy counsel advise on how to handle that uncertainty. What costs trust is certainty that proves to be false later.

Practical check: if you run GitLab on your own Ruby or container image, you also need Ruby 3.3.13, 3.4.12, 4.0.9 or later.

https://www.alpinelinux.org/posts/GitLab-security-incident-CVE-2026-97229.html

#IncidentResponse #DFIR
