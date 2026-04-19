# qf-close — Known Issues & TODO

## Silent Failure Risks (flagged Feb 2026)
These scenarios produce wrong numbers without warning the user:

| Scenario | What happens | Severity |
|----------|-------------|----------|
| Mercury deposit doesn't match a client | Excluded from AR entirely | High |
| Bill.com customer name can't resolve | Recorded as "Unknown" | Medium |
| Bad date in any data source | Silently becomes today's date | Medium |
| Malformed row in rate config | Creates rate with empty client/$0 | High |
| Unknown Mercury account | Labeled "Unknown Account" | Low |
| Optional config tabs missing | Silently skipped | Low |

## Planned Fix
Add a warnings summary at the end of every command output:
- "3 unmatched deposits totaling $X"
- "2 clients billed at $0"
- etc.

Nothing slips through silently.
