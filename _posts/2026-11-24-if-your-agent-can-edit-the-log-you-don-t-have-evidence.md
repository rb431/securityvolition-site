---
layout: post
title: "If Your Agent Can Edit the Log, You Don't Have Evidence"
date: 2026-11-24T12:15:08-05:00
tags: [post, ai-agents, logging, evidence, incident-response, immutable-storage]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7531042737157079041/
source: Ankit Agarwal (@ItsAnkitAg), "Your Agent Can Probably Edit the Log That Would Convict It," https://x.com/ItsAnkitAg/status/2125101330046058790
type: post
---

When an agent goes sideways, IR teams start in a familiar place. They want a trustworthy account of the tools it called and the systems it reached. That is often where things fall apart.

Ankit Agarwal (@ItsAnkitAg) makes a point I most emphatically agree with: too many agent setups store the only narrative of a job in the same workspace the agent uses to do the work. If that file can be rewritten or deleted by the same identity that just ran the job, you do not have an evidentiary record you can trust.

What IR teams are seeing is not subtle. The session history lives in the project folder, beside the code. After a messy run, the transcript is truncated, or it ends neatly in a way that does not match the outbound connections. Sometimes the file is just gone. Then the argument starts. Was this a sloppy cleanup, or did someone use the agent's access to cover their tracks? You cannot settle that from a log the agent could have edited.

Scoping needs a reliable sequence of tool calls. Timeline reconstruction gets mushy if you only have host logs and a partial chat history. You will spend hours trying to tell a software fault from an intrusion when the one place that should have distinguished them is already in doubt.

What I'd want to see:
- Session and tool-call logs forwarded off-host, over syslog or OpenTelemetry, to a SIEM or log platform in a separate logging account or subscription where the agent's identity has no write or delete rights
- A copy in storage the agent cannot alter, such as S3 Object Lock in compliance mode, or immutable storage for Azure Blob Storage with a locked time-based retention policy

If the only copy is on the box the agent lives on, assume it can disappear the moment you need it.

Managers should ask a simpler question before the next pilot expands: if this agent had a bad day tomorrow, could we reconstruct its actions from evidence it had no way to touch?

https://x.com/ItsAnkitAg/status/2125101330046058790

#IncidentResponse #AISecurity #DFIR
