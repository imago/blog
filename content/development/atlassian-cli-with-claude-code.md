---
title: "AI-Driven Development: Jira + Claude Code on macOS"
date: 2026-02-22T15:00:00+01:00
description: Install and configure the Atlassian Jira CLI on macOS via Homebrew, then let Claude Code read your tickets, plan the work, and implement it end-to-end.
tags: [macos, jira, atlassian, claude, ai, workflow, homebrew]
draft: false
---

Modern development teams live in Jira. Claude Code lives in your terminal. This guide wires them together so Claude can read a ticket, ask you clarifying questions, propose a plan, implement the feature, and open a pull request — all without you leaving your editor.

# Prerequisites

* macOS with [Homebrew](https://brew.sh) installed
* A Jira Cloud account (or Jira Data Center ≥ 8.4)
* A GitHub/GitLab repository for the project
* Claude Code installed (`npm install -g @anthropic-ai/claude-code`)

# 1. Install the Jira CLI

The community-maintained `jira-cli` by Ankur Kothiwal is the de-facto standard Jira CLI and is available as a Homebrew tap.

```bash
brew install ankitpokhrel/jira-cli/jira-cli
```

Verify the installation:

```bash
jira version
```

You should see output similar to `jira version 1.x.x`.

# 2. Create an Atlassian API Token

The CLI authenticates via a personal API token, not your password.

1. Go to **https://id.atlassian.com/manage-profile/security/api-tokens**
2. Click **Create API token**, give it a name (e.g. `jira-cli-macos`), and copy the token.

Keep this token — you will not be able to view it again.

# 3. Configure the CLI

Run the interactive initialisation wizard:

```bash
jira init
```

The wizard asks for:

| Prompt | Example value |
|---|---|
| Installation type | `Cloud` |
| Server URL | `https://your-org.atlassian.net` |
| Login (email) | `you@example.com` |
| API token | *(paste the token from step 2)* |
| Default project | `MYPROJ` |

The config is stored at `~/.config/.jira/.config.yml`. You can also set the default project per repository by creating a `.jira/.config.yml` file inside the repo root.

## Verify the connection

```bash
jira issue list --project MYPROJ --status "In Progress" --limit 5
```

A table of open tickets confirms everything is working.

## Useful everyday commands

```bash
# List your assigned tickets
jira issue list --assignee $(jira me) --status "To Do"

# View a ticket in the terminal
jira issue view MYPROJ-42

# Move a ticket to In Progress
jira issue move MYPROJ-42 "In Progress"

# Add a comment
jira issue comment add MYPROJ-42 "Starting implementation."

# Open the ticket in the browser
jira open MYPROJ-42
```

# 4. The Claude Code Workflow

With the Jira CLI in place, Claude Code can query tickets directly via the Bash tool. Claude Code does not auto-discover installed tools — it simply inherits your shell environment (including `PATH`) when it executes commands. Because Homebrew places the `jira` binary in `/opt/homebrew/bin`, which is on your `PATH`, Claude can run `jira` commands the same way you would in a terminal. You can verify this yourself:

```bash
which jira   # /opt/homebrew/bin/jira
```

The workflow below shows how to drive a complete feature from ticket to pull request.

## Step 1 — Pick your ticket

Start a Claude Code session in your repository:

```bash
cd ~/workspace/my-project
claude
```

Ask Claude to list the available work:

```
Show me the open tickets assigned to me in project MYPROJ that are in "To Do" status.
```

Claude runs `jira issue list` under the hood and prints a summary. You reply with the ticket you want to tackle, e.g.:

```
Let's work on MYPROJ-42.
```

## Step 2 — Claude reads the ticket and enters plan mode

```
Read the full description of MYPROJ-42 and enter plan mode.
```

Claude fetches the ticket with `jira issue view MYPROJ-42`, parses the acceptance criteria, explores the codebase, and enters **plan mode**. In plan mode Claude does not write any code yet — it only gathers information and proposes a design.

## Step 3 — Interactive Q&A until the plan fits

Claude will ask clarifying questions before finalising the plan. Typical questions look like:

> *The ticket mentions a REST endpoint for `/api/reports`. Should this follow the existing pattern in `src/api/` using FastAPI routers, or is a separate service more appropriate here?*

> *Acceptance criterion 3 says "export to CSV". Should the file be streamed directly to the browser or first written to S3?*

Answer each question in plain language. Claude incorporates your answers into the plan document. Once you are happy with the proposed approach, approve it:

```
The plan looks good. Proceed with implementation.
```

## Step 4 — Implementation

Claude leaves plan mode and begins writing code. It follows the plan step by step, running tests after each logical chunk. You can watch the progress in real time and interrupt at any point:

```
Stop — use the existing `ReportBuilder` class rather than creating a new one.
```

Claude adjusts mid-flight and continues.

## Step 5 — Create the pull request

Once all tests pass, ask Claude to open a PR:

```
Create a pull request for this feature. Link ticket MYPROJ-42 in the PR description.
```

Claude runs `gh pr create` (GitHub CLI) with a description that includes:

* A summary of what was implemented
* A link back to the Jira ticket
* A test plan checklist

It then moves the Jira ticket to **In Review**:

```bash
jira issue move MYPROJ-42 "In Review"
jira issue comment add MYPROJ-42 "PR opened: <pr-url>"
```

# 5. Putting It All Together — A Sample Session

```
You: List my To Do tickets in MYPROJ.

Claude: Found 3 tickets assigned to you:
  MYPROJ-38  Add pagination to /api/users          (Story, 3 pts)
  MYPROJ-42  CSV export for the reports screen      (Story, 5 pts)
  MYPROJ-45  Fix N+1 query in dashboard loader      (Bug)

You: Let's work on MYPROJ-42. Read it and enter plan mode.

Claude: [reads ticket, explores codebase, enters plan mode]

  I have a few questions before finalising the plan:
  1. The reports endpoint currently lives in `src/api/reports.py`. Should
     the CSV export be a new route on the same router, e.g. GET
     /api/reports/export?
  2. Should large exports be streamed (chunked transfer encoding) or
     generated fully in memory first?
  3. The ticket mentions "date range filter" — should this reuse the
     existing `DateRangeFilter` Pydantic model or accept raw query params?

You: Yes, same router. Stream it. Reuse DateRangeFilter.

Claude: [updates plan, presents final outline for approval]

  Plan:
  1. Add GET /api/reports/export route to existing router
  2. Accept DateRangeFilter as query params
  3. Stream CSV rows using StreamingResponse
  4. Add unit tests for the new endpoint
  5. Update OpenAPI docs

You: Looks good. Implement it.

Claude: [implements, runs tests, all green]

You: Create a PR and move the ticket to In Review.

Claude: PR opened at https://github.com/org/repo/pull/87
        Ticket MYPROJ-42 moved to "In Review".
```

# 6. Tips

* **Per-project config** — Drop a `.jira/.config.yml` in your repo root with `project: MYPROJ` so you never have to pass `--project` manually.
* **Secure token storage** — Store the API token in your macOS Keychain and reference it via an environment variable rather than hard-coding it in the config file.
* **Custom JQL** — Claude can execute arbitrary JQL via `jira issue list --jql "..."` for complex queries like sprint membership or label filters.
* **Multiple accounts** — The CLI supports named profiles in `~/.config/.jira/` so you can switch between a work and a personal Atlassian account.

# Further Resources

* jira-cli GitHub: https://github.com/ankitpokhrel/jira-cli
* jira-cli documentation: https://github.com/ankitpokhrel/jira-cli/tree/main/docs
* Atlassian API token management: https://id.atlassian.com/manage-profile/security/api-tokens
* Claude Code documentation: https://docs.anthropic.com/claude-code
