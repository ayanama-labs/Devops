# 1. Getting Started with Git

---

## 1.1 About Version Control

### What is Version Control?

**Version control** is a system that records changes made to files over time so that you can:

- See what changed
- See who changed it
- Go back to an older version
- Compare different versions
- Work with other developers
- Experiment without destroying the stable version

Think of it as a **history system for your project**.

### Without Version Control

Imagine you are building an application:

```text
project/
├── final.zip
├── final2.zip
├── final_latest.zip
├── final_latest_real.zip
└── final_latest_real_updated.zip
```

This becomes difficult to manage.

With Git:

```text
Project History

Commit A → Commit B → Commit C → Commit D
```

You can move through the history whenever necessary.

---

### Why Version Control Is Important

#### 1. History

You can know how the project reached its current state.

```text
A → B → C → D
```

#### 2. Recovery

If version D breaks the application:

```text
A → B → C → D ❌
```

You can inspect or restore an earlier state.

#### 3. Collaboration

Multiple developers can work on the same project.

```text
Developer A ──┐
               ├──→ Project
Developer B ──┘
```

#### 4. Experimentation

You can create a separate line of development without disturbing the main project.

```text
              ┌── Feature experiment
              │
A ── B ── C ── D
```

This concept eventually becomes **branching**.

---

### Types of Version Control

Historically, version-control systems evolved through several models.

#### Local Version Control

History is stored locally.

```text
Computer
   ↓
Version Database
```

Example:

- RCS

Problem: collaboration is difficult.

---

#### Centralized Version Control

One central server stores the repository.

```text
          Server
         /  |  \
        /   |   \
      Dev1 Dev2 Dev3
```

Examples:

- SVN
- CVS
- Perforce

Problem:

> If the central server goes down, collaboration and many operations are affected.

---

#### Distributed Version Control

Every developer has a **complete repository**, including history.

```text
       Developer A
       Full History
            ↕
       Central Server
            ↕
       Developer B
       Full History
```

Git is a **distributed version control system (DVCS)**.

This is one of the most important things to understand about Git.

---

# 1.2 A Short History of Git

Git was created in **2005** by **Linus Torvalds**, the creator of Linux.

### Why was Git created?

The Linux kernel was being developed by thousands of contributors.

Before Git, Linux development used a version-control system called **BitKeeper**.

Eventually, the relationship between the Linux community and BitKeeper broke down.

The Linux community needed a new version-control system.

Linus Torvalds created Git.

### Original goals of Git

Git was designed to be:

- Fast
- Distributed
- Reliable
- Capable of handling huge projects
- Suitable for thousands of contributors
- Able to handle branching and merging efficiently

Git was initially developed extremely quickly in 2005.

Today, Git is one of the dominant version-control systems in software development.

---

# 1.3 What is Git?

Git is a **distributed version control system**.

But let's break that definition down.

### Version Control

Git records changes to your project.

### Distributed

Every cloned Git repository contains its own history.

For example:

```text
Your Computer
└── Git Repository
    ├── Current files
    └── Complete history
```

You don't need the internet for many Git operations.

For example:

```bash
git log
```

can work without an internet connection because the history exists locally.

---

## Git ≠ GitHub

This distinction is extremely important.

### Git

Git is a **program/tool** for version control.

```text
Git
 ↓
Tracks project history
```

### GitHub

GitHub is a **hosting and collaboration platform** built around Git repositories.

```text
Git
 ↓
Local repository
 ↓
GitHub
 ↓
Remote repository
```

Other Git hosting platforms include:

- GitLab
- Bitbucket
- Codeberg

So:

> **Git is the technology. GitHub is a service that uses Git.**

---

# 1.4 The Command Line

Git can be used through different interfaces.

### Command Line

You interact with Git using commands:

```bash
git status
git add .
git commit
git log
```

The command line is extremely important because it exposes what Git is actually doing.

---

## Basic Command Structure

Most Git commands follow:

```bash
git <command> [options] [arguments]
```

Example:

```bash
git status
```

Here:

```text
git       → Git program
status    → command
```

Another example:

```bash
git commit -m "Add login page"
```

```text
git          → program
commit       → command
-m           → option
"Add..."     → argument
```

---

## Why Learn Git Through CLI?

Graphical interfaces are convenient, but the CLI gives you:

- Better understanding
- More control
- Automation possibilities
- Access to advanced features
- Consistency across environments

For a programmer, you should be comfortable with both:

```text
Git CLI
   +
Git GUI
```

But learn the CLI first.

---

# 1.5 Installing Git

Git must first be installed on your operating system.

### Check whether Git is already installed

```bash
git --version
```

Example:

```text
git version 2.x.x
```

If you get a version number, Git is installed.

If the command isn't recognized, Git isn't properly installed or isn't available in your `PATH`.

---

## Windows

You can install **Git for Windows**.

It normally includes:

- Git
- Git Bash
- Git CLI
- Git Credential Manager

After installation:

```bash
git --version
```

---

## Linux

For Debian/Ubuntu:

```bash
sudo apt update
sudo apt install git
```

Then:

```bash
git --version
```

---

## macOS

Git can be installed using package managers such as Homebrew:

```bash
brew install git
```

---

# 1.6 First-Time Git Setup

After installing Git, configure your identity.

Git uses your identity when creating commits.

### Configure username

```bash
git config --global user.name "Your Name"
```

### Configure email

```bash
git config --global user.email "you@example.com"
```

Example:

```bash
git config --global user.name "Ayan"
git config --global user.email "ayan@example.com"
```

---

## What does `--global` mean?

Git configuration can exist at different levels.

### Global

Applies to your user account:

```bash
git config --global
```

### Local

Applies only to the current repository:

```bash
git config
```

Conceptually:

```text
System
   ↓
Global
   ↓
Local repository
```

More specific configuration can override broader configuration.

---

## Check Your Configuration

```bash
git config --list
```

Or:

```bash
git config user.name
git config user.email
```

---

## Why Does Git Need Your Name and Email?

Consider this commit:

```text
Commit
├── Changes
├── Date
├── Author
└── Commit message
```

Git needs to know who created the commit.

Example:

```text
Author: Ayan <ayan@example.com>
Message: Add authentication
```

This becomes part of the project's history.

---

# 1.7 Getting Help

Git has built-in documentation.

### General help

```bash
git help
```

### Help for a command

```bash
git help commit
```

or:

```bash
git commit --help
```

### Quick command help

```bash
git commit -h
```

The distinction is useful:

```bash
git commit -h
```

usually gives a short usage summary.

```bash
git help commit
```

provides much more detailed documentation.

---

## You Don't Need to Memorize Every Git Command

Git has hundreds of commands/options.

Instead, remember the important workflow:

```text
Understand
   ↓
Check status
   ↓
Make changes
   ↓
Stage changes
   ↓
Commit changes
   ↓
Inspect history
```

Then use:

```bash
git help <command>
```

when you encounter something unfamiliar.

---

# 1.8 Summary

The most important concepts from this chapter are:

### Version Control

A system for tracking changes to files over time.

### Git

A **distributed version control system**.

### Repository

A Git repository contains your project and its Git history.

Conceptually:

```text
Project
   +
Git History
   =
Git Repository
```

### Git vs GitHub

```text
Git
 ↓
Version control software

GitHub
 ↓
Online hosting/collaboration platform
```

### Git is Distributed

A cloned repository contains its own history.

```text
Local Repository
      +
Complete History
```

Therefore, many Git operations don't require internet access.

### Git CLI

Basic structure:

```bash
git <command> <options> <arguments>
```

### Installation

Verify Git:

```bash
git --version
```

### Initial Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Help

```bash
git help <command>
```

or:

```bash
git <command> --help
```

---

# 🧠 Mental Model to Keep

Don't memorize Git as a collection of commands yet. Think of it as:

```text
                 GIT
                  │
          Version Control
                  │
       ┌──────────┴──────────┐
       │                     │
    Track                 History
    Changes                 │
       │              ┌──────┴──────┐
       │              │             │
    Working         Commits       Branches
    Files              │             │
       │              │             │
       └──────────────┴─────────────┘
                      │
                  Repository
                      │
                  ┌───┴───┐
                  │       │
                Local   Remote
                  │       │
                 Git    GitHub
```

The **next chapter should make this mental model concrete** by teaching the core Git workflow:

```text
Working Directory
       ↓
     git add
       ↓
Staging Area
       ↓
   git commit
       ↓
Repository
```

That **three-part model—working directory → staging area → repository—is the fundamental idea you should understand before trying to memorize Git commands.**
