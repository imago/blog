---
title: "Using Obsidian with Claude Code on macOS"
date: 2026-02-22T09:47:33+01:00
description: "A guide to setting up Obsidian with the Kanban plugin and using it to manage tasks with Claude Code."
tags: [obsidian, claude-code, macos, kanban, workflow]
draft: false
---

Obsidian is a powerful Markdown-based knowledge base that works on top of a local folder of plain text files. Combined with its Kanban plugin and Claude Code, you can create a lightweight project management workflow where you track tasks on a board and delegate development work to Claude.

This guide walks through the full setup on macOS.

## Prerequisites

- macOS with Homebrew installed.
- Basic familiarity with the terminal and Markdown.

## Installing Claude Code and Obsidian

Install both applications via Homebrew:

```bash
brew install --cask obsidian
```

```bash
brew install claude-code
```

After installation, launch Obsidian from Spotlight (Cmd + Space, type "Obsidian", press Enter). On first launch, Obsidian will ask you to create or open a vault. A vault is simply a folder on your filesystem where Obsidian stores all your notes as `.md` files.

Create a new vault by clicking **Create new vault**, giving it a name (e.g., "Projects"), and choosing a location on disk. Obsidian will create the folder and open it.

Verify Claude Code is available by running:

```bash
claude --version
```

## Setting up the Kanban Board

The Kanban plugin lets you create drag-and-drop task boards directly inside Obsidian. Each board is stored as a regular Markdown file in your vault.

### Install the Kanban plugin

1. Open Obsidian and go to **Obsidian > Settings > Community plugins**.
2. Click **Turn on community plugins** if prompted.
3. Click **Browse** to open the community plugin catalog.
4. Search for **Kanban** (by mgmeyers) and click **Install**.
5. After installation completes, click **Enable** to activate the plugin.

### Create a new board

1. Open the Command Palette with **Cmd + P**.
2. Type **Kanban: Create a new board** and select it.
3. Give the board a name (e.g., "Sprint Board") and press Enter.

The board opens in Kanban view. You can also find the Kanban icon in the left sidebar to create new boards from there.

### Configure your columns

A typical board for use with Claude Code uses these columns (lists):

- **Funnel** — incoming ideas and raw tasks.
- **In Progress** — tasks currently being worked on.
- **In Review** — pull requests waiting for review.
- **Done** — completed and merged work.

To add a column, click **Add a list** at the right side of the board and type the column name. Repeat for each column.

### Add and manage cards

- Click **Add a card** at the bottom of any column to create a new task.
- Write a short, descriptive title (e.g., "Add dark mode toggle to settings page").
- Drag cards between columns to update their status.
- Click on a card to open it and add detailed notes, acceptance criteria, or links to related files.

## Working with Claude Code

The Kanban board acts as your task tracker. The general workflow is:

### 1. Create a task in Funnel

Add a card to the **Funnel** column with a clear description of the work to be done. Include enough context so the task is self-contained, for example:

> Add a new blog post about setting up Neovim on macOS. Include installation via Homebrew, basic configuration in init.lua, and plugin setup with lazy.nvim.

### 2. Move the task to In Progress

Drag the card to **In Progress** when you are ready to start working on it.

### 3. Assign the task to Claude Code

Open your terminal, navigate to the project repository, and start Claude Code:

```bash
cd /path/to/your/repo
claude
```

Describe the task to Claude. Claude will read the codebase, make the necessary changes, and create a commit. You can ask Claude to create a pull request:

```
Create a PR for the changes you just made.
```

Once the PR is created, move the card to **In Review**.

### 4. Review and merge the PR

Review the pull request on GitHub. If the changes look good, merge the PR into `main`. Move the card to **Done**.

If changes are needed, leave review comments on the PR and ask Claude to address them:

```
Address the review comments on PR #42.
```

### Summary

| Step | Action | Board Column |
|------|--------|--------------|
| 1 | Write task description | Funnel |
| 2 | Start working on task | In Progress |
| 3 | Claude creates a PR | In Review |
| 4 | Merge PR after review | Done |
