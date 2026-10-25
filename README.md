# inQdo team ops agent

A Claude Code plugin that checks the Jira service desk queues of the clients your team supports. It tells you what is new or changed since the last check, flags P1 tickets and checks that the Daily Check task has been picked up.

**Read-only.** The plugin never comments on, assigns or transitions tickets.

## What you get

| Command | Purpose |
|---|---|
| `/ops-setup` | One-time: create your local config with your clients and Jira filters |
| `/ops-check [client ...] [morning\|lunch\|afternoon]` | Run the check. Without arguments it checks all your clients and picks the slot from the time |

Run `/ops-check` at the start of your workday, before lunch and around 16:00. Each run compares against the previous one.

Each client is reviewed by its own agent run and reported in its own section. Client information is never mixed in one report.

## Install

Prerequisites: Claude Code, and the Atlassian (Jira) connector authorised for your inQdo account.

```
/plugin marketplace add inqdo-internal/inqdo-team-ops-agent
/plugin install inqdo-team-ops@inqdo-team-ops
/ops-setup
```

The agent allows the connector tool names of the claude.ai Atlassian connector (`mcp__claude_ai_Atlassian_Rovo__*`). If you use a different Atlassian MCP server, adjust the `tools` line in `agents/queue-reviewer.md`.

## Confidentiality

This repository is public, so it contains **no client names, project keys or ticket data**. The real configuration lives in `~/.config/inqdo-team-ops/clients.json` on your machine. Get the project names and filters from your team, or let `/ops-setup` ask you.

Please keep it that way:

- Never commit `clients.json` or anything from the `state` folder. `.gitignore` already excludes them.
- The state file only holds ticket keys and update timestamps, no names or ticket text.
- Reports contain customer data and colleague names. Do not paste them into public or cross-client channels.

## How a check works

1. Run the client's queue filter and the Daily Check filter.
2. Compare with the state of the last check to find new, updated and gone tickets.
3. Sort tickets into P1, Daily Check, automated notifications, waiting for us, waiting for others.
4. Report. For every open P1: age, whether the one-hour response target is met, assignee and a draft Jira comment to inform the client team and account manager. The draft is never posted. You paste it into the ticket yourself.

The rules are in `skills/ops-triage/SKILL.md`. Change them there, for example the noise patterns or how stale a waiting ticket must be.

## Roadmap

- Write actions, such as taking a ticket or adding a comment, after the team has agreed on the rules.
- Scheduled runs.
