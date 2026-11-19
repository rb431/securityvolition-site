---
layout: post
title: Qubole Leftover Vendor Roles
date: 2026-11-19T09:10:01-05:00
tags: [dfir, cloud-ir, aws, third-party-risk]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7529184209559638016/
source: https://www.securityweek.com/over-a-dozen-organizations-hit-in-qubole-supply-chain-attack/
type: post
---

Quick question for your next cloud review: which vendors can still assume a role in your AWS accounts, and are you still paying all of them?

From Oct 14 to 26, someone holding stolen Qubole IAM credentials walked through the cross-account roles customers had created for Qubole. More than a dozen organizations were hit, per Sentra. A lot of those roles belonged to companies that had stopped using Qubole long ago. Nobody deleted them. The attacker enumerated each account, pulled secrets from the usual spots, tested them with TruffleHog, and in some cases downloaded entire S3 buckets.

External IDs didn't help. Sentra saw roles with valid External IDs assumed. That condition protects you from a confused deputy and does nothing once the vendor's own credentials are stolen.

So the fix is boring. Offboard vendor access the way you'd offboard an employee.

The minimum controls needed here:
- an inventory of every role that trusts an outside AWS account, with an owner and a contract tied to it
- role deletion as a line item when a vendor relationship ends
- least privilege on what those roles can read (does the analytics vendor really need every bucket?)
- an alert when a vendor role that's been quiet for 90 days suddenly gets assumed

Ever had a Qubole role? Hunt CloudTrail back to Oct 1 for AssumeRole sessions named quiam- (like quiam-enum or quiam-scope-full).

https://www.securityweek.com/over-a-dozen-organizations-hit-in-qubole-supply-chain-attack/

#CloudSecurity #IncidentResponse #AWS
