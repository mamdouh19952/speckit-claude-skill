<p align="center">
  <img src="assets/banner.png" alt="Spec-Kit Skill for Claude Code" width="100%">
</p>

<h1 align="center">Spec-Kit Skill for Claude Code</h1>

<p align="center">
  Turn your idea into well-structured code with <b>Spec-Driven Development</b> inside Claude Code,<br>
  step by step, without memorizing a single command.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-Plugin-D97757?style=for-the-badge" alt="Claude Code Plugin">
  <a href="https://github.com/github/spec-kit"><img src="https://img.shields.io/badge/GitHub-Spec--Kit-24292f?style=for-the-badge&logo=github" alt="GitHub Spec-Kit"></a>
  <img src="https://img.shields.io/badge/macOS_%7C_Windows-supported-58a6ff?style=for-the-badge" alt="macOS | Windows">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-3fb950?style=for-the-badge" alt="MIT License"></a>
</p>

<p align="center">
  <a href="README.md">العربية</a> · <b>English</b>
</p>

---

## Overview

[GitHub Spec-Kit](https://github.com/github/spec-kit) is GitHub's official toolkit for *Spec-Driven Development*: you start with a clear specification, then a technical plan, then small tasks, and only then the implementation. Using it well, though, means knowing its commands, their order, and the right flags for each one.

This skill handles all of that for you. Tell Claude what you want to build, and it sets up the tooling, initializes the project, and guides you through each stage in order, pausing at every key stage for your review.

> [!NOTE]
> This skill complements the official Spec-Kit CLI; it doesn't replace it. The CLI itself is installed on your machine as described below.

## Features

- **Automatic tooling setup**: checks for `uv` and `specify` and installs them when missing.
- **Correct project initialization**: runs `specify init` with the right flags for Claude Code, in a new or an existing project.
- **Ordered stages**: from the project constitution through implementation, with no step skipped.
- **Review gates**: no code is written until you approve the spec and the plan.
- **Project-aware**: if Spec-Kit is already set up, it goes straight to the new feature without re-initializing.
- **Arabic and RTL support**: a dedicated checklist for right-to-left UI quality.

## Workflow

<p align="center">
  <code>constitution</code> → <b><code>specify</code></b> ✅ → <code>clarify</code> → <b><code>plan</code></b> ✅ → <b><code>checklist</code></b> ✅ → <code>tasks</code> → <code>analyze</code> → <code>implement</code>
</p>

The stages marked ✅ are **review gates**: Claude summarizes what was produced and waits for your approval before moving on.

---

## Requirements

| Tool | Purpose |
|---|---|
| **Claude Code** | Where the skill runs |
| **Git** | Required to install `specify` from GitHub |
| **uv** | Python tool manager; installs `specify` and downloads Python automatically if needed |
| **specify-cli** | The Spec-Kit CLI itself |

<details>
<summary><b>Install on macOS</b></summary>

<br>

Open **Terminal** and run:

```bash
# 1) Git (if not installed)
xcode-select --install

# 2) Claude Code
curl -fsSL https://claude.ai/install.sh | bash

# 3) uv
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Close and reopen Terminal, then:

```bash
# 4) specify-cli
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

</details>

<details>
<summary><b>Install on Windows</b></summary>

<br>

Open **PowerShell** and run:

```powershell
# 1) Git (if not installed)
winget install --id Git.Git -e

# 2) Claude Code
irm https://claude.ai/install.ps1 | iex

# 3) uv
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Close and reopen PowerShell, then:

```powershell
# 4) specify-cli
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

</details>

**Verify the installation:**

```bash
git --version && claude --version && uv --version && specify --version
```

> [!TIP]
> If you get `specify: command not found`, run `uv tool update-shell` and open a new terminal.

---

## Installation

### Option 1: As a plugin (recommended)

Open Claude Code (run `claude` in your terminal) and enter:

```
/plugin marketplace add mamdouh19952/speckit-claude-skill
/plugin install speckit@speckit-claude-skill
```

Then restart Claude Code. To get the latest version later:

```
/plugin marketplace update speckit-claude-skill
```

### Option 2: Manual copy

Copy the `plugins/speckit/skills/speckit-workflow` folder into one of these locations:

| Scope | macOS | Windows |
|---|---|---|
| All projects | `~/.claude/skills/` | `%USERPROFILE%\.claude\skills\` |
| One project | `<project>/.claude/skills/` | `<project>\.claude\skills\` |

<details>
<summary>Copy commands</summary>

<br>

**macOS:**

```bash
git clone https://github.com/mamdouh19952/speckit-claude-skill.git
mkdir -p ~/.claude/skills
cp -R speckit-claude-skill/plugins/speckit/skills/speckit-workflow ~/.claude/skills/
```

**Windows (PowerShell):**

```powershell
git clone https://github.com/mamdouh19952/speckit-claude-skill.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills"
Copy-Item -Recurse speckit-claude-skill\plugins\speckit\skills\speckit-workflow "$env:USERPROFILE\.claude\skills\"
```

</details>

---

## Usage

Open Claude Code in your project folder and describe what you want in plain language, mentioning **speckit**:

```
Build a small online store with Laravel + Blade using speckit
```

```
Add a monthly reports feature to this project using speckit
```

### Supported scenarios

The skill detects your project's state automatically:

| Scenario | What the skill does |
|---|---|
| **New project** (empty folder) | Initializes Spec-Kit → project constitution → first feature |
| **Existing project**, no Spec-Kit yet | Reads the current code → safe init that leaves your files untouched → constitution derived from your existing code style → the new feature |
| **Project already using Spec-Kit** | Goes straight to `/speckit-specify` with no new init or constitution, and creates the next feature folder in `specs/` (e.g. `specs/004-coupons/`) |

> [!TIP]
> In an existing project without a `CLAUDE.md`, the skill suggests running `/init` first so Claude understands the codebase. It's optional but helpful.

### Spec-Kit commands

| # | Command | Purpose |
|:-:|---|---|
| 1 | `/speckit-constitution` | Project principles, **once** per project |
| 2 | `/speckit-specify` | Feature description → `specs/001-name/spec.md` |
| 3 | `/speckit-clarify` | Questions that resolve ambiguities in the spec *(optional, recommended)* |
| 4 | `/speckit-plan` | Technical plan and data model |
| 5 | `/speckit-checklist` | Requirements quality review (UX, RTL, accessibility) |
| 6 | `/speckit-tasks` | Breaks the plan into small, actionable tasks |
| 7 | `/speckit-analyze` | Checks that spec, plan, and tasks are consistent *(optional)* |
| 8 | `/speckit-implement` | Executes the tasks and writes the code |

You don't need to type these yourself; Claude runs them in order.

**Tips:**
- Give each complete feature (login, products, cart...) its own `/speckit-specify`.
- Fast path: constitution → specify → plan → tasks → implement.
- For an Arabic project, mention RTL in the feature description and the skill will run its RTL checklist.

---

## Good to know

- **Commands after init**: in the same session that ran `specify init`, the `/speckit-...` commands don't appear in the menu yet. That's expected, and Claude runs the stages from their files directly. From the next session on, the commands are available.
- **Checklist items**: generated unchecked on purpose so you review them yourself. Claude shows them to you and only starts implementing after you approve.
- **Command naming**: older Spec-Kit versions use a dot (`/speckit.specify`), newer ones a hyphen (`/speckit-specify`). Both are the same command.
- **Init command**: the skill runs `specify init --here --force --non-interactive --integration claude`. Without `--integration claude`, a non-interactive init defaults to GitHub Copilot; without `--force`, it refuses to run in a non-empty folder.

---

## FAQ

<details>
<summary><b>What's the difference between <code>constitution.md</code> and <code>CLAUDE.md</code>?</b></summary>

<br>

| File | Created by | Contents |
|---|---|---|
| `.specify/memory/constitution.md` | `/speckit-constitution` | Project principles: coding, testing, and security rules |
| `CLAUDE.md` | Claude Code (`/init`) or you | Context Claude reads every session: the stack, run commands |

The two are independent; `specify init` neither creates nor modifies `CLAUDE.md`.

</details>

<details>
<summary><b>Does <code>specify init</code> modify my existing files?</b></summary>

<br>

No. It only adds the `.specify/` folder and the Spec-Kit commands under `.claude/skills/`. It doesn't touch your code, `CLAUDE.md`, or `.claude/settings.json`, and re-running it keeps your constitution and `specs/` folder as they are.

</details>

<details>
<summary><b>Does it work with ChatGPT?</b></summary>

<br>

No. It's built specifically for **Claude Code**'s skills and plugins system, and there's no direct way to install it in ChatGPT.

</details>

---

## License

Released under the [MIT](LICENSE) license © Mamdouh.
