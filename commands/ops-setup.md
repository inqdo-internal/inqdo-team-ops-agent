---
description: Create your local ops-monitor configuration for the clients your team supports.
argument-hint: "[client names]"
---

Set up the local configuration for the ops queue monitor. This file stays on this machine and must never be committed or pasted into a public place, because it names clients and projects.

1. If `~/.config/inqdo-team-ops/clients.json` exists, show which client keys it contains and ask whether to change it. Do not overwrite without confirmation.
2. Read the plugin's `config/clients.example.json` as the template.
3. Ask the user for each client: a short key, the display name, and the exact Jira project name. Use the standard queue filter from the template unless the user gives a different one. The Daily Check filter matches summaries containing "Daily Check" in that project.
4. Find the Jira site with the Atlassian connector (`getAccessibleAtlassianResources`) and use its host name as `jiraSite`.
5. Validate each filter with `searchJiraIssuesUsingJql` in count mode, and report the counts per client in separate lines.
6. Write `~/.config/inqdo-team-ops/clients.json` and create the `state` directory next to it.
