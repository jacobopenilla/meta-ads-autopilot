# Meta Ads + Shopify autopilot

A nightly routine that audits a small e-commerce operation without anyone watching: it reads Meta Ads campaigns and Shopify orders, applies a written playbook of kill rules, and emails a short report. Built with Claude Code routines and MCP connectors, used on real cash-on-delivery campaigns in Colombia.

This repo documents the design and the lessons. It contains no credentials, account IDs or client data.

## What it does

Every night at 11:30 pm Colombia time (04:30 UTC), a cloud routine:

1. **Reads Meta Ads**: campaigns, ad sets, ads, spend and CPA for the last days.
2. **Reads Shopify**: orders and store status, to check that sales and ad data tell the same story.
3. **Applies the playbook**: each campaign is checked against kill rules (spend without results, CPA above break-even, creative-level limits). A rule counts as triggered at **90% of its threshold**, so a campaign is not left running until it crosses 100%.
4. **Acts within limits**: it can pause what the playbook says to pause. Reactivating is only allowed through explicit exceptions written in the playbook.
5. **Reports**: one plain-text email with one line per product, a line for new-batch alerts, a line for Shopify, and a closing line. See [docs/report-format.md](docs/report-format.md).

## Architecture

```
Scheduler (cron, UTC)
      │
      ▼
Cloud routine (Claude Code) ──► Meta Ads connector (MCP)
      │                    ──► Shopify connector (MCP)
      │                    ──► Playbook (kill rules, versioned)
      ▼
Gmail connector ──► one plain-text report
```

More detail in [docs/architecture.md](docs/architecture.md).

## Design decisions

- **A written playbook, not ad-hoc judgment.** The rules live in a versioned document the routine reads, so behavior is predictable and changes are reviewable.
- **Fire at 90%, not 100%.** Learned the hard way: a campaign sitting at 91% of its kill threshold was killed by hand. The rule now fires early and automatically.
- **Plain-text email as the only output channel.** Chat bots and push notifications were tried and dropped (see lessons).
- **Turning things on is the exception.** Pausing is automatic; reactivating only happens under explicit rules in the playbook, everything else is a human decision.

## Lessons from running it

- **Test the output channel in the real environment.** A messaging bot failed with a network block in the sandbox, and an early Gmail connector run hung the sandbox. The email path was only trusted after a live test run completed end to end.
- **Watch the time zone.** A host machine set to the wrong time zone shifted the clock by about 15 hours. The cron is defined in UTC (11:30 pm Colombia = 04:30 UTC) and the host time zone is now checked.
- **A dense report does not get read.** The first email was long and full of markdown symbols. Cutting it to about 12–15 lines of plain text made it usable.
- **When ad data and store data disagree, check the setup before the code.** Meta showed purchases that Shopify did not have. The cause was a store that had been rebuilt while the ad account kept its history, not a tracking bug. A low event match quality score turned out to be small sample size, not a missing setting.

## Status

Used in production on cash-on-delivery e-commerce campaigns. Being adapted for service clients.

## License

Documentation only.
