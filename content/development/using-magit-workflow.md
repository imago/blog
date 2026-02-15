---
title: "Using Magit with Emacs"
date: 2026-02-15T11:58:42+01:00
description: A quick guide on how to set up and use Magit with Emacs, including integration with Gitflow.
tags: [emacs, git]
draft: true
---

## Prerequisites

You need the following on macOS before you start:

- Homebrew installed (package manager for macOS).
- emacs installed
- git installed

If you do not have Homebrew yet, install it from the official Homebrew website before proceeding.

## First steps using Magit with Emacs

7. Open Emacs and install Magit:

```bash
M-x package-install RET magit-gitflow RET
```

8. Open Magit with:

```bash
M-m g
```

9. To test Magit:
   - After modifying your source code, stash your changes with s and commit with c.
   - Write a commit message and quit with C-c and C-c
   - Push your changes with P and p

---

## Using Gitflow with Magit

Now, let’s use the **Gitflow workflow**.

1. Install gitflow-avh:
    https://github.com/petervanderdoes/gitflow-avh/wiki/Installation

Example for macOS:

```bash
brew install git-flow-avh
```

Then use it in the terminal with:

```bash
git flow <subcommand>
```

2. In Emacs, install the package exec-path-from-shell:

```bash
M-x package-install RET exec-path-from-shell RET
```

3. Add the following lines to your .emacs configuration:

```bash
(require 'magit-gitflow)
(add-hook 'magit-mode-hook 'turn-on-magit-gitflow)
```

4. Workflow steps:
   - Modify some code.
   - Open Magit: C-x g
   - In the Magit window, type C-f to initialize Gitflow.
   - Create and publish a new feature branch.
   - Finish the feature branch and publish changes to the develop branch (do not forget this step, because the feature branch will be deleted without a merge to develop).

Further reading: https://github.com/nahidulhasan/git-flow-example
