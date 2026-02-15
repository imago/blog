---
title: "Using Magit with Emacs"
date: 2026-02-15T11:58:42+01:00
description: A quick guide on how to set up and use Magit with Emacs.
tags: [emacs, git]
draft: false
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

