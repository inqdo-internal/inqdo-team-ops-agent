---
description: Check the Jira queues of your clients for new and updated tickets, P1s and the Daily Check. Read-only.
argument-hint: "[client-key ...] [morning|lunch|afternoon]"
---

Run an operations queue check. Arguments: `$ARGUMENTS`

1. Read `~/.config/inqdo-team-ops/clients.json`. If it does not exist, tell the user to run `/ops-setup` and stop.
2. Pick the clients: the client keys given in the arguments, otherwise all clients in the config.
3. Pick the slot: `morning`, `lunch` or `afternoon` if given. Otherwise by local time: before 11:00 morning, 11:00 to 14:00 lunch, after 14:00 afternoon.
4. Launch the `queue-reviewer` agent once per client, in parallel, each with its client key, the slot and the config path. One agent per client, never one agent for several clients.
5. Show each agent's report as its own section, one after the other, without merging, comparing or summarising across clients.
6. If any report contains a P1, repeat only the P1 headline per client at the very end ("P1 open: <KEY> <client>"), so it is not missed.

Do not modify any Jira ticket.
