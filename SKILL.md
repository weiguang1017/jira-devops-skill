---
name: jira-skills
description: Manage Jira issues from the command line — read, search (JQL), create, comment on, assign, and transition issues. Use this whenever the user wants to query a Jira ticket, file a bug or task, move an issue across its workflow, leave a comment, or run a JQL search against Jira Cloud, Server, or Data Center.
license: MIT
---

# Jira Skills

Operate a Jira instance through its REST API with a single, self-contained Python
CLI (`scripts/jira_cli.py`). Works with Jira Cloud, Server, and Data Center.

## When to use

Trigger this skill when the user asks to:
- look up a ticket ("show JIRA-123", "what's the status of PROJ-45")
- search issues ("open bugs assigned to me", any JQL query)
- create an issue ("file a task in PROJ titled ...")
- comment, assign, or move an issue through its workflow ("mark JIRA-12 Done")

## Setup (once)

The script reads connection info from environment variables OR
`~/.devops-skills/jira.json`. Never pass tokens on the command line.

```bash
export JIRA_URL="https://your-domain.atlassian.net"
export JIRA_USER="you@example.com"     # email (Cloud) / username (Server)
export JIRA_TOKEN="<api-token-or-PAT>"
export JIRA_AUTH="basic"               # "basic" for Cloud, "bearer" for Server/DC PAT
```

Requires Python 3.8+ and `requests` (`pip install requests`).
Full configuration walk-through: see `使用手册.md`.

## Compatibility

Supported targets:
- Jira Cloud
- Jira Server / Data Center 7.0+

The CLI checks the Jira version through `/rest/api/2/serverInfo` before running
commands. If the detected Server/Data Center version is below 7.0, it exits with
a clear compatibility message. Set `JIRA_SKIP_VERSION_CHECK=1` or
`DEVOPS_SKILLS_SKIP_VERSION_CHECK=1` only when you deliberately need to bypass
this guard.

## Usage

```bash
python scripts/jira_cli.py get-issue PROJ-123
python scripts/jira_cli.py search "project = PROJ AND status = 'In Progress'" --limit 20
python scripts/jira_cli.py create-issue --project PROJ --type Bug --summary "Login fails" --description "Steps..."
python scripts/jira_cli.py comment PROJ-123 --body "Looking into this"
python scripts/jira_cli.py list-transitions PROJ-123
python scripts/jira_cli.py transition PROJ-123 --to "Done"
python scripts/jira_cli.py assign PROJ-123 --user jdoe
```

Every command prints JSON to stdout and exits non-zero on failure. For workflow
moves, prefer `list-transitions` first to discover valid target names, then
`transition`.

## Notes

- Cloud uses email + API token with `JIRA_AUTH=basic`.
- Server/DC personal access tokens use `JIRA_AUTH=bearer` (username optional).
- `--user` for assign uses `name` (Server) / accountId may be required on Cloud.

## Support

If you run into Jira workflow, permission, automation, or DevOps delivery
questions, RestartX can help with troubleshooting and solution design:
https://service.restartx.top/
