# Scope Document and Rules of Engagement

**Engagement:** Hypothetical web application penetration test against `*.intel.com`
**Prepared by:** Raja Wardet Wahaj (Wolf)

This document is written so it would survive legal review: a wildcard scope with a named exclusion, a break-glass clause, and an emergency procedure.

## Scope

| | |
|---|---|
| **In scope** | `*.intel.com` (all current and future subdomains); the published API hosts; the IP range confirmed in writing by the client as belonging to that zone. |
| **Out of scope** | `career.intel.com` (named exclusion); any host resolving outside the agreed IP range; any third-party service pointed at the zone but not owned by the client. |
| **Prohibited techniques** | No denial of service; no social engineering; no destructive actions (delete / encrypt / corrupt). |
| **Access granted** | Two test accounts (one per role); VPN profile; jump-server details — classifies this as a grey-box web test. |
| **Testing window** | [dates] 20:00–06:00 PKT, outside business hours, to protect production. |
| **Emergency contact** | [Named person], [direct number], agreed stop procedure. |

## Break-glass clause

A wildcard includes every subdomain created during the engagement and every third-party service someone points at the zone. Before any newly discovered host is tested, the named client contact must authorise it in writing, and that authorisation is recorded against the host in the engagement log. No new host is touched until that record exists.

## Rules of engagement

| Rule | Why it exists |
|---|---|
| Written authorisation before the first packet | Without it, testing is a criminal offence, not an assessment. |
| Test only what is listed | Everything else belongs to someone else. |
| Data minimisation | Prove access with one record and stop; never copy the database. |
| No destructive actions unless authorised | "Test it" never implies delete, encrypt or corrupt. |
| Stop on critical finding | Notify immediately rather than waiting for the report. |
| Destroy test data afterwards | Confirm in writing when and how it was destroyed. |
