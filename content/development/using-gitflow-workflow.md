---
title: "Mastering Gitflow on macOS"
date: 2026-02-15T14:58:42+01:00
description: A comprehensive guide to setting up and using Gitflow on macOS for streamlined branching workflows.
tags: [git, gitflow, macos, workflow]
draft: false
---

Gitflow is a proven branching model originally developed by Vincent Driessen. It provides a robust framework for managing features, releases, and hotfixes in software projects. This guide shows you how to set up Gitflow system-wide on macOS.

# What is Gitflow?

Gitflow defines a strict branching workflow with two main branches:

* **main (or master)**: Always contains production-ready code
* **develop**: Integration branch for new features

Additionally, there are temporary branches for:

* **Feature branches**: For new functionality (branched from develop)
* **Release branches**: For release preparation (branched from develop)
* **Hotfix branches**: For critical production fixes (branched from main)

Gitflow Cheatsheet: https://danielkummer.github.io/git-flow-cheatsheet/

# Installing Gitflow on macOS

The AVH edition of Gitflow is the actively maintained community version with additional functionality and performance improvements over the original implementation.

Via Homebrew (recommended)
```bash
brew install git-flow-avh
```

Verification
```bash
git flow version
```



# Using Gitflow in the Terminal

## Initialize a Repository

Navigate to your Git repository and initialize Gitflow:

```bash
cd /path/to/your/repo
git flow init -d
touch README.md
git add README.md
git commit -am"Initial commit"
git remote add origin https://github.com/OWNER/REPOSITORY.git
git push -u origin main
git checkout -b develop
git push -u origin develop
```

The -d flag accepts all default values (main as production branch, develop as development branch).

Please set the default branch to "develop" (e.g. within Settings of branch on github).


## Feature Workflow

Create a new feature branch:

```bash
git flow feature start amazing-feature
```

This automatically creates a feature/amazing-feature branch from the current develop branch.

Work on your feature and commit as usual:

```bash
git add .
git commit -m "Add: Implement core functionality for amazing feature"
```


Publish the feature branch (for teamwork):

```bash
git flow feature publish amazing-feature
```

Finish the feature by a PR or if no PR required:

```bash
git flow feature finish amazing-feature
```


This merges the feature branch into develop and deletes the local feature branch.

### Create a release

```bash
git flow release start 1.2.0
git flow release publish 1.2.0
```

Please open a PR and merge changes and delete old branch or:

```bash
git flow release finish 1.2.0
```

Afterwards, do

```bash
git push origin main
git push origin develop  
git push origin --tags
```

### Create a hotfix

```bash
git flow hotfix start critical-fix
git flow hotfix publish critical-fix
git flow hotfix finish critical-fix
```
And push changes
```bash
git push origin main develop --tags
```


# Further Resources
## Gitflow AVH Edition on GitHub: 
https://github.com/petervanderdoes/gitflow-avh

## Gitflow AVH Wiki: 
https://github.com/petervanderdoes/gitflow-avh/wiki


