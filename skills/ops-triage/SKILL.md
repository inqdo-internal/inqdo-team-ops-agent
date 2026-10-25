---
name: ops-triage
description: Triage rules and report format for the inQdo operations queue check. Used by the queue-reviewer agent and the /ops-check command.
---

# Ops triage

## Time slots

| Slot | Compare against |
|---|---|
| morning | The last check of the previous workday (state file). Without state, the last 24 hours. |
| lunch | The morning check of today. |
| afternoon | The lunch check of today. |

If the state file is older than the slot implies, say so in one line instead of guessing. Always report the actual window used, with timestamps.

## Classification

Assign every ticket to exactly one bucket, first match wins:

1. **P1**: priority name starts with the configured P1 prefix (default "P1"). Always at the top, regardless of status or age.
2. **Daily Check**: summary starts with "Daily Check". Reported separately, never in the queue lists.
3. **Automated noise**: summary matches one of `noiseSummaryPatterns`. Collapse into one line per kind, with count and worst status (for example "12 notifications, 3 Warning, 9 OK"). List individual keys only for Warning, Critical or Error. Notifications with status OK are never listed individually.
4. **Waiting for us**: status "Waiting for Support", "Open" or similar where the next step is ours.
5. **Waiting for others**: status "Waiting for Customer" or "Waiting for Supplier". Mention only if updated since the last check or untouched for more than 7 days.
6. **Other**.

Diff against the state file: a ticket is **new** if its key is not in state, **updated** if its `updated` timestamp is newer than in state, and **gone** if it was in state but no longer matches the filter (resolved or moved). Report gone tickets by key only.

## P1 handling

The response target for P1 is one hour from creation. Resolution has a longer SLA, but in practice the team drops other work and fixes P1 immediately.

For each open P1:

- State the age and whether the one-hour response target is met, breached or still running. A response is a comment by an inQdo colleague, so check the comments. If you cannot tell, say that.
- Say who it is assigned to, or "unassigned".
- Add a **draft Jira comment** the colleague can paste into the ticket to inform the rest of the client team and the account manager. We inform people with a comment on the ticket, not by email. Keep it to two or three lines: what is happening, who has picked it up, and the response status. Leave the @mentions for the colleague to add. Draft only. This tool is read-only and never posts anything.

A P1 that is unassigned or past the response target is the first line of the whole report.

## Daily Check

Use the newest ticket from `dailyCheckJql`.

- Morning slot: if no Daily Check ticket exists with today's date in its summary, say "Daily Check for today not created yet". If it exists but is unassigned, say "not picked up yet". Otherwise give the assignee and status.
- Lunch and afternoon slot: report only if today's Daily Check is still not Done.
- Always compare the date in the summary with today's date, not with the created date.

## Report format

Plain markdown, in this order, skipping empty sections. One client per report, with the client name as the title.

```
# <Client> | <slot> check | <date time>
Window: <from> to <to>

## Needs action now
<P1 items with draft Jira comment, breached response targets>

## Daily Check
<one line>

## New since last check
<table: key, type, priority, status, summary>

## Updated since last check
<table: key, status, priority, assignee, updated>

## Waiting for us
<table, only items not already listed above>

## Waiting for customer or supplier
<only items that are stale or changed>

## Automated notifications
<one line per kind>

## Gone from the queue
<keys>

Queue total: <n> open (<n> new, <n> updated, <n> gone)
```

Keep tickets in one table row each, link every key to the ticket URL, and keep summaries to one line. No commentary beyond what helps a colleague decide what to open first.

## State file

`~/.config/inqdo-team-ops/state/<client>.json`:

```json
{
  "lastCheck": "2026-01-01T08:05:00+01:00",
  "slot": "morning",
  "tickets": { "KEY-1": "2026-01-01T07:30:00.000+0100" }
}
```

Store only keys and `updated` timestamps of tickets currently matching the filter. Replace the file on every run.
