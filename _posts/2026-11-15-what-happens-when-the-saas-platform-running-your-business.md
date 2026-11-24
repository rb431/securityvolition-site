---
layout: post
title: "What happens when the SaaS Platform Running Your Business Goes Dark?"
date: 2026-11-15T12:15:06-05:00
tags: [article, business-continuity, saas-outage, third-party-risk, microsoft-365, workday, salesforce, ehr]
linkedin_url: https://www.linkedin.com/pulse/what-happens-when-saas-platform-running-your-business-brackett-tn1je/
type: article
---
Around 12:25 PM ET on Thursday, Microsoft 365 started falling over. Outlook, Teams, SharePoint and Copilot were disrupted worldwide, and Microsoft didn't say service was fully restored until Saturday. Microsoft blamed an issue from an earlier network capacity upgrade and said there was no sign of a cyberattack. Downdetector took reports from more than 70 countries. In the UK, NHS staff fell back on paper records. Now, platforms like O365 are so massive, they are subject to a special failure mode that Eric S. Raymond has coined as a "[hot-state dependency](https://x.com/esrtweet/status/2121339860824797635)" so the lessons there don't necessarily apply across the board.

Then at about 5:15 PM ET Friday, Workday put customer systems into maintenance. Workday says that while it was validating a weekly disaster recovery maintenance window, services at its DR site unexpectedly connected to production databases and left data in an inconsistent state. Employees at affected employers couldn't clock in or check their pay, and some were in the middle of open enrollment. As of Saturday morning, Workday hadn't announced a restoration time. Read that again. A DR test caused the outage.

This keeps happening in every category:

- CRM: On Sept 16, Salesforce had a disruption across multiple instances in all regions for roughly seven and a half hours, traced to an internal login service eating server resources. Some customers couldn't even open a support case, and scheduled jobs didn't run as expected after access came back.
- EHR: On Feb 19, 2026, a ransomware attack took the University of Mississippi Medical Center's Epic EHR offline along with its phones and email. UMMC closed its roughly 35 clinics statewide until March 2, and its hospitals documented care and orders on paper.
- Healthcare payments: The Change Healthcare ransomware attack in February 2024. In an AHA survey, 94% of hospitals reported financial impact and 74% reported direct impact on patient care.
- Dealer software: Ransomware at CDK Global in June 2024 sent dealerships back to paper forms for about two weeks.

## Start with business processes, then find the apps

Most BCPs list applications. Flip that. It's not the app that matters, it's the work. Which processes hurt badly if they stop for a day? Taking orders, paying people, scheduling patients, closing the month. Map each one to the SaaS platforms underneath it, including the hidden dependencies. Email alone sits under approvals, password resets, invoice routing and half your vendor notifications.

## Your RTO is not your vendor's SLA

A 99.9% monthly uptime SLA still allows about 43 minutes of downtime a month, and the remedy is usually service credits. Credits don't make payroll. Set your recovery time objective from business impact, then plan for the vendor blowing past it. Same for recovery point. If the vendor's data comes back inconsistent (Workday's words), how far back can you reconcile from your own copy?

## Write down degraded mode before you need it

The bar here:
- Scheduled offline exports of the data each critical process needs: CRM contacts and open opportunities, tomorrow's patient schedules and active med lists, the last payroll register, stored encrypted and somewhere other than the platform that just went down (and be sure you can use that data when you can't load it back into the platform that isn't available)
- Manual workflows people have actually practiced, with printed forms that don't live only in SharePoint
- A named decision owner and a written trigger for switching to manual, something like "two hours down with no vendor ETA and we go to paper"
- A plan for who re-enters the manual work once the platform is back

Hospitals have downtime procedures because they've needed them. Finance and sales teams mostly don't. How about deciding now that if payroll is down on payday, you pay everyone off the last register and true it up next cycle? Get finance and HR to sign off now, not at 5 PM on a Friday.

## When email and Teams are the outage

This is the one that bites incident response hardest. Your crisis comms plan probably runs through Outlook and Teams, amiright? On Thursday it didn't work.

What works:
- An out-of-band channel on a different provider, set up in advance with leadership already in it
- An offline contact list with mobile numbers for leadership, IT, key vendors and your account reps
- Vendor status pages and support numbers saved outside your SSO, because the vendor's support portal may be down too
- Pre-written notices for employees and customers that say what you know and when you'll update next

## Outage or compromise? Know before you reconnect

Microsoft and Workday were operational failures. Change Healthcare, CDK and UMMC were ransomware. That changes the playbook. When a vendor outage is cyber-caused, reconnecting the minute they say "restored" can bring the problem back into your environment. What IR teams are seeing: the smart customers keep integrations offline until the vendor provides indicators of compromise and a clear statement of scope. Review the vendor's API keys, service accounts and network paths into your environment, rotate credentials, and only then turn things back on.

## Contracts, concentration and tabletops

Read your contracts for what the vendor owes you in a sustained outage: notification timelines, data export rights and DR commitments. Then count how many critical processes hang off one provider. If your email, files, chat and meetings all live with one vendor, Thursday showed you what a single failure domain looks like.

Tabletop a vendor outage this quarter using this week's real timeline. Thursday afternoon, no email or Teams and no ETA. Friday evening, the payroll and timekeeping platform goes into maintenance. Who decides to go manual? How do people even find out? If you want to keep your team's hands-on skills sharp between tabletops, I keep a running list of events on the CTF Board at https://ctf.securityvolition.com.

## Recovery is its own project

The platform coming back is where the second half starts. Microsoft extended its email retry window to 48 hours, so late mail kept arriving after users were back. Salesforce customers had scheduled jobs that didn't fire. Reconcile manual records against the system, rerun missed jobs and integrations, check for duplicate transactions, and document the gap for auditors. Then hold a lessons-learned with the business owners who ran on paper.

Your vendors will have bad days. This week two of the biggest had them back to back. Build your plan as if yours is next.

#BusinessContinuity #IncidentResponse #ThirdPartyRisk #DFIR #Resilience

## Sources

- Microsoft 365 outage (AP): https://apnews.com/article/microsoft-outage-outlook-email-365-recover-ca395c0f1e51af784b6d70afb6554f6a
- Microsoft 365 outage (CNBC): https://www.cnbc.com/2026/11/12/microsoft-outage-outlook-aws-google.html
- Workday outage: https://www.webpronews.com/workday-outage-disaster-recovery-test-production-data/
- Salesforce outage (CIO): https://www.cio.com/article/4223037/salesforces-massive-outage-exposes-the-hidden-risks-of-cloud-dependencies.html
- UMMC Epic ransomware (Healthcare Dive): https://www.healthcaredive.com/news/university-of-mississippi-medical-center-ransomware-attack/812823/
- UMMC Epic ransomware (BleepingComputer): https://www.bleepingcomputer.com/news/security/mississippi-medical-center-reopens-clinics-hit-by-ransomware-attack/
- Change Healthcare (AHA survey): https://www.aha.org/2024-03-15-aha-survey-change-healthcare-cyberattack-significantly-disrupts-patient-care-hospitals-finances
- CDK Global (Reuters): https://www.reuters.com/technology/cybersecurity/ripple-effect-cdk-hack-widens-more-us-auto-dealers-flag-hit-2024-06-24/

## Related

- [[2026-11-10 LinkedIn Post 2 - When Your RMM Vendor Goes Dark]]
- [[2026-11-10 LinkedIn Post 1 - Keep the Admin UI Off the Internet]]
- [[2026-11-09 LinkedIn Article - AI-Speed Exploitation and Compensating Controls]]
