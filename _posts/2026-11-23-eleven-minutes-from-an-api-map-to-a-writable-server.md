---
layout: post
title: Eleven Minutes From an API Map to a Writable Server
date: 2026-11-23T13:21:43-05:00
tags: [article, ai-agents, zero-day, monai-label, cairn, vulnerability-management, research-servers]
linkedin_url: https://www.linkedin.com/pulse/eleven-minutes-from-api-map-writable-server-richard-brackett-5fwse/
source: https://aws.amazon.com/blogs/security/chinese-nexus-actors-cairn-ai-agents-found-a-zero-day-on-their-own/
type: article
---

Eleven Minutes From an API Map to a Writable Server

Amazon Threat Intelligence published a report on November 20 that every manager who owns a lab, a research network, or a forgotten server should read. A threat actor pointed an open-source AI penetration-testing framework called Cairn at a list of university IP addresses and gave the agents one goal: "obtain shell access." The agents found a previously unknown path-traversal bug in an open-source medical imaging tool. In about 11 minutes they went from mapping the application's API to writing files wherever the server process could write ([Amazon Threat Intelligence](https://aws.amazon.com/blogs/security/chinese-nexus-actors-cairn-ai-agents-found-a-zero-day-on-their-own/)). In a later session they used the same bug to plant a Python reverse shell and get a root prompt. (Here are a couple of questions: 1. Why was that app publicly available to anyone? And 2. Was the web service process running as root already?)

That's Amazon's account of a real campaign, reconstructed from a server the operator left sitting open on the internet. Here's a walk-through of what the report actually shows, what it doesn't, and what a mid-size organization should do about it.

How Amazon found it

On September 10, Amazon Threat Intelligence found a publicly hosted Cairn instance running with no authentication. Anyone who found the server could read the operational history: project configurations, the operator's prompts, and the agents' output. The prompts were written in Simplified Chinese, and timestamps in the executed scripts were set to China Standard Time.

Amazon says the instance documented a campaign against IP addresses associated with universities in East Asia outside mainland China, lasting about 48 hours and beginning September 8. The actor supplied the target list and variations of that "obtain shell access" goal. From there, Amazon says, the agents planned, executed, and adjusted as they learned, without the operator choosing each next step.

Cairn was built as a research and problem-solving tool for defenders. You give it a starting point and a goal in plain language, and it coordinates multiple AI agents to find a path. That same ease of use is why a threat actor can run it. Amazon also points to separate research, from Gambit Security, documenting a financially motivated actor using Cairn against online retailers.

The agents failed more often than they succeeded

The agents spent a lot of time getting nowhere, and that belongs in the story too.

The longest operation targeted an unauthenticated Redis instance at one university. Over roughly 16 hours and 148 sequential actions, the agents tried a long list of known techniques for taking over an open Redis server, then tried Log4Shell against another service on the same system. When those failed, they used an SSRF bug in a co-located Qdrant instance to map an internal Docker network and sprayed credentials at PostgreSQL, SSH, and other services. None of it worked.

A separate operation ran more than 6 hours against a university GPU server. The agents built a list of about 80 real usernames from public sources: faculty directories, lab sites, archived pages, and academic papers. Then they tested more than 30,000 credential combinations against SSH on that server, and against an admin dashboard, a network storage device, a VPN, and the campus portal on other systems. They never found a working login.

So these agents are persistent, and they are also fallible. They will grind for the better part of a day without a coffee break. That doesn't mean every target falls.

The 11-minute path, and the later root shell

Against a server running an open-source medical imaging AI application, the outcome was different. The agents identified the framework, mapped its API endpoints, and found an upload endpoint with a filename parameter. They tested that parameter for path traversal, and it was vulnerable. Amazon's clock on that stretch is about 11 minutes, from mapping the API to arbitrarily writing files.

The root shell did not happen in those 11 minutes. In a later session, the agents used the same vulnerability to write a reverse-shell payload into sitecustomize.py, a file Python imports automatically when the interpreter starts. They then triggered a training API endpoint so a new Python process would start, import that file, and open a reverse shell running as root.

Amazon assesses with high confidence that this was a zero-day at the time of discovery, based on the absence of a corresponding fix, issue, advisory, or prior mention in vulnerability databases for the project.

Then a human stepped back in. After the agents found the zero-day, the operator provided a proxy that tunneled into a second university network. The server on the other end of that proxy was running the same vulnerable software, and Amazon infers the actor had used the zero-day to compromise it and set up the tunnel. That second compromise is an inference, not a confirmed reconstruction of the same quality as the first. The actor then directed the agents to use the tunnel against other machines in that network.

Amazon did not name the universities, and the report does not say patient images or clinical systems were taken. Don't fill that gap with a worse story than the one we have. A research or labeling server with a root shell is already bad enough.

What the software is, and where the fix stands

Amazon's blog describes the target as an open-source medical imaging AI application and does not name the product. The project's own advisory, originally reported by AWS's vulnerability disclosure program, identifies it as MONAI Label, a tool research teams use to annotate medical images and train models ([MONAI Label advisory](https://github.com/Project-MONAI/MONAILabel/security/advisories/GHSA-qc22-rmgc-vg94)).

The advisory rates the bug critical and says it affects versions 0.8.5 and earlier. The `PUT /datastore/image` endpoint takes a caller-controlled image parameter and writes the uploaded file without checking that the path stays inside the datastore. A value such as `../../../tmp/x` writes wherever the MONAI Label process can write. The label-upload endpoint has the same pattern. Authentication is off by default (`MONAI_LABEL_AUTH_ENABLE=False`), so in the default configuration any network client that can reach the server can exploit it. If authentication is on, the ANNOTATOR role is enough.

The issue is now tracked as CVE-2026-133968, published November 20 and scored 9.8 ([OSV](https://osv.dev/vulnerability/CVE-2026-133968)). The GitHub advisory page still shows a blank CVE field, which looks like a lag in updating that page rather than a second bug.

Here is the part managers need to hear clearly. Amazon says the maintainers released a fix and a GitHub security advisory. That is true, and it is easy to misread. The code fix was merged to the project's main branch on October 19 in pull request 1915 ([PR 1915](https://github.com/Project-MONAI/MONAILabel/pull/1915)). The advisory, published November 18, initially listed no patched version. On November 22 the maintainers released MONAI Label 0.8.6, and that is now the patched version on GitHub and PyPI ([0.8.6 release](https://github.com/Project-MONAI/MONAILabel/releases/tag/0.8.6)). Upgrade to 0.8.6. It requires Python 3.11 or newer, and it also fixes a second path-traversal bug in session uploads. If a 0.8.5 or older server was reachable from an untrusted network, review it for unexpected file changes before putting it back into service. The workarounds still matter after you upgrade: turn authentication on and keep the ANNOTATOR role away from untrusted users, keep the server off untrusted networks, and run it as an unprivileged user whose write access is limited to the datastore.

The affected organizations were not AWS customers, and AWS says it had no direct path to notify them. It worked with a government partner to get word to the organizations. Amazon also published two IP addresses seen in records dated September 8 to 10: 142.4.98.145 and 142.4.107.34.

Amazon assesses with moderate confidence that this is a Chinese-nexus threat actor, based on the Simplified Chinese prompts, the China Standard Time timestamps, and the targeting of universities it describes as likely of strategic interest to China. Moderate confidence is not a courtroom finding. Treat the techniques as real and the attribution as Amazon's current assessment.

What is actually new here

Path traversal in an upload function is an old class of bug. An unauthenticated lab tool on the internet is an old class of mistake. How about we not pretend either of those started this year?

The tempo is what changed. One operator set a goal and let agents work for 16 hours against one target, then find a zero-day and reach arbitrary file write in about 11 minutes on another. A human still chose the targets and, later, provided the tunnel into the second network. The agents still failed more than they succeeded. The new problem is that the successful path is now fast enough, and cheap enough to retry, that an obscure research server can be mapped and written to before your team has finished triaging the first alert.

This case also shows why patch windows matter more now. Vendors and researchers are under pressure to disclose serious bugs faster, because AI is speeding up discovery and exploit development. The other side of that clock is the time until a defender can actually install a fix. AWS reported this bug, the maintainers merged a patch in October, and the installable release did not land until November 22. For about a month, anyone who installed MONAI Label the usual way was still on a vulnerable build. That gap is now a period when automated agents can already be trying the same class of bug against whatever you left reachable.

In practice, that means:
- Build a living inventory of research, lab, and annotation servers, including GPU boxes, labeling tools, model-training APIs, Redis, vector databases, and anything a graduate student or vendor stood up "just for a project."
- Treat internet exposure as the first question, not the last. If a host does not need to be reachable from the public internet, take it off or put it behind a VPN or jump path.
- Turn authentication on for every tool that ships with it disabled, and do not grant broad roles to shared accounts.
- Give each of those hosts a named owner, a data classification, and a patch path, including how you will upgrade when a security release ships, and what you will do when the package index is still behind.

If you work in healthcare, a university, or a lab that collaborates with either, start with medical imaging and research stacks. MONAI Label is one example. The pattern is any open-source service that was meant to run on a trusted network and quietly ended up with a public address.

Open-source exposure works the same way. You cannot patch what you do not know you run, and research software is often installed from a README, pinned once, and left alone for a year. Watch the project's advisory and release feed, not only your enterprise scanner's default catalog. When the advisory says "fixed in main, next release pending," that is a compensating-control week, not a reason to wait.

Detection still works when the attacker is a machine

You are not going to inspect every prompt an agent thought about. You can still catch the outcomes.

What I'd want to see:
- Egress controls and alerting on lab and research subnets, especially unexpected outbound shells, new reverse-SSH tunnels, and connections to unfamiliar IPs. A reverse shell from a labeling server should not need a committee meeting to block.
- File-integrity and startup-hook checks on Python services, including sitecustomize.py, .pth files, cron, and authorized_keys, because that is how this root shell was planted.
- Alerts on long-running authentication spray and unusual API mapping against internet-facing apps, even when the spray fails. The failed Redis and GPU operations were noisy. They also ran for hours.
- High-confidence detections that trigger an automated action, with a human reviewing the outcome. Amazon's own guidance is that an agent which finds and exploits a zero-day in minutes finishes faster than many security operations centers can manually triage an alert.

A machine-speed attacker does not get a special pass through your firewall. It still has to write a file, start a process, and call home. Those are the same artifacts IR teams already hunt. The difference is how little time you have after the first one appears.

What an IR team would do

If you run MONAI Label, or you find any internet-facing research annotation or training service you had forgotten, treat this as a reason to look NOW, including whether you can upgrade to 0.8.6.

Confirm the version and whether authentication is enabled. If the host is on 0.8.5 or earlier and reachable, assume it may have been probed since early September. Pull it off the internet or put it behind an authenticated path, upgrade to 0.8.6, and apply the workarounds. If it was reachable, check it for unexpected file changes.

Then scope it like any other web-app compromise that can lead to a write-anywhere and a root shell:
- Look for unexpected files in application directories, Python startup hooks, cron, and SSH authorized keys, and for the training API being used in ways that do not match normal lab work.
- Review outbound connections from the host around the campaign window, and any proxy, tunnel, or reverse-shell traffic to the addresses Amazon published or to other unusual destinations.
- Ask whether this host could reach other research systems, storage, or identity services. Amazon's second-university account is an inference, but the lesson holds: once an agent has a tunnel, the next targets are internal.
- Inventory every other instance of the same software, including copies standing in a student's cloud project or a vendor's demo.

Document what you can prove and what you cannot. If you do not have logs, say so, and fix the logging before you declare the incident closed.

TLDR

Amazon is describing a campaign in which autonomous agents, given a short goal and a list of university addresses, found an unpatched bug in a research imaging tool and took a server to root. The attribution is moderate. The 11-minute clock covers the path to arbitrary file write, not the later root shell. A patched package is now out: 0.8.6, released November 22. If you are still on 0.8.5 or earlier, upgrade, and check the host for unexpected file changes if it was reachable.

Managers do not need a new AI-defense platform to start. They need a list of the research and lab systems their organization actually runs, a rule that those systems are not public by default, authentication turned on, egress watched, and a way to apply a workaround when the vendor's next release has not shipped yet, then upgrade as soon as it does. The agents will keep trying. Your job is to make the easy path disappear, and to notice quickly when something still gets through.

If your team wants hands-on practice hunting reverse shells, scoping a compromised web app, and reading process trees, check out the DFIR CTF Board at https://ctf.securityvolition.com for a running list of DFIR and blue-team CTFs.

Sources:
Amazon Threat Intelligence, Chinese-nexus actor's Cairn AI agents found a zero-day on their own (Nov 20, 2026): https://aws.amazon.com/blogs/security/chinese-nexus-actors-cairn-ai-agents-found-a-zero-day-on-their-own/
MONAI Label, GHSA-qc22-rmgc-vg94 (Nov 18, 2026): https://github.com/Project-MONAI/MONAILabel/security/advisories/GHSA-qc22-rmgc-vg94
MONAI Label, pull request 1915, merged Oct 19, 2026: https://github.com/Project-MONAI/MONAILabel/pull/1915
MONAI Label 0.8.6 security release (Nov 22, 2026): https://github.com/Project-MONAI/MONAILabel/releases/tag/0.8.6
OSV, CVE-2026-133968: https://osv.dev/vulnerability/CVE-2026-133968
SecurityWeek, Amazon: China-Linked Hackers' AI Agents Exploited Medical Software Zero-Day (Nov 21, 2026): https://www.securityweek.com/amazon-china-linked-hackers-ai-agents-exploited-medical-software-zero-day/

#IncidentResponse #AISecurity #VulnerabilityManagement
