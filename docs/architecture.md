# Architecture

## Components

| Piece | Role |
| --- | --- |
| Scheduler | Cron expression in UTC. 11:30 pm Colombia is 04:30 UTC. |
| Cloud routine | A Claude Code routine with a fixed prompt: read, check, act, report. |
| Meta Ads connector | Reads campaigns, ad sets, ads and insights. Can pause (and, under playbook exceptions, reactivate) entities. |
| Shopify connector | Reads orders and shop info. Read-only. |
| Playbook | A versioned document with the kill rules and the checklist the routine follows. |
| Gmail connector | Sends the final report. |

## Run flow

1. Start the routine and load the playbook.
2. Pull Meta data for the active campaigns.
3. Pull Shopify orders for the same period.
4. For each campaign, compare spend and CPA with the rule thresholds. Mark a rule as triggered at 90% of its threshold.
5. Pause what the playbook says to pause. Reactivate only under explicit exceptions written in the playbook.
6. Detect new batches of ads that need a first review.
7. Build the report and send it by email.

## Guardrails

- Pause by default; reactivate only under explicit playbook exceptions.
- Shopify access is read-only.
- No credentials or IDs in the prompt text that is published here.
- The report is the single source of what happened that night. If no email arrives, treat it as a failure and check the run.

## Failure modes seen

- Run hangs right after the process starts: no tool calls, no notification.
- Messaging bot blocked by the sandbox network.
- Wrong host time zone, which looked like a wrong cron time.
- Ad platform and store disagree because the store was rebuilt.
