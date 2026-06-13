<p align="center">
  <img src="assets/logo.png" alt="Archon" width="160" />
</p>

<h1 align="center">Archon + GDIT-SDAF</h1>

<p align="center">
  Deterministic AI coding workflows with enterprise security and compliance.
</p>

<p align="center">
  <a href="https://trendshift.io/repositories/13964" target="_blank"><img src="https://trendshift.io/api/badge/repositories/13964" alt="coleam00%2FArchon | Trendshift" style="width: 250px; height: 55px;" width="250" height="55"/></a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="License: MIT" /></a>
  <a href="https://github.com/coleam00/Archon/actions/workflows/test.yml"><img src="https://github.com/coleam00/Archon/actions/workflows/test.yml/badge.svg" alt="CI" /></a>
  <a href="https://archon.diy"><img src="https://img.shields.io/badge/docs-archon.diy-blue" alt="Docs" /></a>
</p>

---

> **Quick Start:** Jump to [Setup & Installation](#setup--installation) to get started in ~15 minutes.

## About This Version

This is Archon with **GDIT-SDAF** (General Dynamics IT Secure Development Automation Framework) - an enterprise-ready extension that adds:

- **Security scanning** - bandit, safety, semgrep, trivy integrated into workflows
- **Compliance tracking** - NIST SSDF attestation and evidence generation
- **Branch protection** - mandatory PR reviews, security validation gates
- **GitLab + GitHub support** - auto-detection and unified forge workflows
- **10+ GDIT skills** - standardized patterns for secure development

Archon is a workflow engine for AI coding agents. Define your development processes as YAML workflows - planning, implementation, validation, code review, PR creation - and run them reliably across all your projects.

Like what Dockerfiles did for infrastructure and GitHub Actions did for CI/CD - Archon does for AI coding workflows. Think n8n, but for software development.

## Setup & Installation

### Step 1: Install Prerequisites

**Required for all users:**

1. **Bun** (JavaScript runtime) - [bun.sh](https://bun.sh)
   ```bash
   # macOS/Linux
   curl -fsSL https://bun.sh/install | bash
   
   # Windows (PowerShell)
   irm bun.sh/install.ps1 | iex
   ```

2. **Claude Code** (AI coding assistant) - [claude.ai/code](https://claude.ai/code)
   ```bash
   # macOS/Linux/WSL
   curl -fsSL https://claude.ai/install.sh | bash
   
   # Windows (PowerShell)
   irm https://claude.ai/install.ps1 | iex
   ```

3. **Python 3.12+** (for GDIT security scanners)
   ```bash
   python3 --version  # Should show 3.12.0 or later
   ```

4. **Git with remote configured**
   ```bash
   git remote -v  # Should show origin pointing to GitHub or GitLab
   ```

**Choose your forge CLI:**

**For GitHub users:**
```bash
# macOS
brew install gh

# Windows (via winget)
winget install GitHub.cli

# Linux (Debian/Ubuntu)
sudo apt install gh

# Authenticate
gh auth login
```

**For GitLab users:**
```bash
# macOS
brew install glab

# Linux/Windows - see https://gitlab.com/gitlab-org/cli

# Authenticate
glab auth login
```

### Step 2: Install Archon

**From source (recommended for GDIT-SDAF):**

```bash
git clone https://github.com/coleam00/Archon
cd Archon
bun install
```

**Or use pre-compiled binaries:**

```bash
# macOS / Linux
curl -fsSL https://archon.diy/install | bash

# Windows (PowerShell)
irm https://archon.diy/install.ps1 | iex

# Homebrew
brew install coleam00/archon/archon
```

> **Note:** Compiled binaries require setting `CLAUDE_BIN_PATH` environment variable:
> ```bash
> export CLAUDE_BIN_PATH="$HOME/.local/bin/claude"
> ```
> Or set `assistants.claude.claudeBinaryPath` in `~/.archon/config.yaml`.

### Step 3: Configure Archon

Run the interactive setup wizard:

```bash
# From the Archon repo (source install)
bun run cli setup

# Or if using compiled binary
archon setup
```

The wizard will prompt you for:

1. **AI Assistant Selection**
   - Claude (recommended)
   - Codex (optional)
   - Pi (optional, community provider)

2. **Platform Connections** (all optional)
   - **GitHub** - for issue/PR automation via webhooks
   - **GitLab** - for issue/MR automation via webhooks  
   - Telegram - for remote chat access
   - Slack - for workspace integration

3. **Forge Provider Confirmation**
   
   The wizard auto-detects your forge from git remote:
   - URL contains `gitlab` → GitLab
   - Otherwise → GitHub
   
   You'll be prompted to confirm and the wizard will:
   - Install the appropriate CLI (gh or glab) if missing
   - Run authentication (gh auth login or glab auth login)
   - Test the connection

4. **Security Configuration**

   For GitHub:
   - Personal Access Token (fine-grained)
     - Issues: Read and write
     - Pull requests: Read and write
     - Contents: Read
   - Webhook secret (auto-generated)
   - Allowed users (optional whitelist)
   - Bot mention name (optional)

   For GitLab:
   - Personal Access Token with `api` scope
   - GitLab instance URL (defaults to gitlab.com)
   - Webhook secret (auto-generated)
   - Allowed users (optional whitelist)
   - Bot mention name (optional)

Configuration is saved to `~/.archon/.env` (home-scoped) or `<project>/.archon/.env` (project-scoped).

### Step 4: Set Up Your Project

Navigate to your project and run the GDIT-SDAF onboarding workflow:

```bash
cd /path/to/your/project
archon workflow run meridian-onboard
```

This automated workflow (5-10 minutes):

✓ **Detects your forge** - Auto-configures for GitHub or GitLab  
✓ **Verifies CLI auth** - Ensures gh/glab authentication is working  
✓ **Installs security scanners** - bandit, safety, semgrep, trivy via pip  
✓ **Copies GDIT scripts** - Adds 20+ security and compliance scripts to `.archon/scripts/`  
✓ **Installs GDIT skills** - Adds 10+ Claude Code skills to `.claude/skills/`  
✓ **Creates config files** - Generates `.archon/config.yaml` with forge settings  
✓ **Generates report** - Produces verification report with next steps

**What gets created:**

```
your-project/
├── .archon/
│   ├── config.yaml              # Forge provider: github or gitlab
│   ├── scripts/                 # 20+ security & compliance scripts
│   │   ├── security-scan.py
│   │   ├── ssdf-attestation.py
│   │   └── ...
│   └── workflows/               # Optional: custom workflows
│       └── ...
├── .claude/
│   └── skills/                  # 10+ GDIT skills
│       ├── gdit-sdaf-git-dev-workflow/
│       ├── gdit-sdaf-ssdf-compliance-mapping/
│       └── ...
└── docs/                        # Created if missing
```

### Step 5: Verify Installation

Check that everything is configured correctly:

```bash
archon doctor
```

This validates:
- ✓ Claude binary path and version
- ✓ gh or glab CLI authentication
- ✓ Database connectivity (SQLite by default)
- ✓ Workspace directory writability
- ✓ Bundled workflows and commands loaded
- ✓ Platform adapter connectivity (if configured)

All checks should pass. If any fail, run `archon setup` again to reconfigure.

### Step 6: Start Using Archon + GDIT-SDAF

**From the command line:**

```bash
# List available workflows (includes GDIT-specific workflows)
archon workflow list

# Run a workflow
archon workflow run gdit-sdaf-fix-issue "Fix authentication bug"

# Or use Claude Code with the archon skill
claude
```

Then in Claude Code:
```
Use archon to fix issue #42 with security validation
```

**Using GDIT skills directly in Claude Code:**

```
/gdit-sdaf-git-dev-workflow - Guided commit, branch protection validation, PR creation
/gdit-sdaf-ssdf-compliance-mapping - Map changes to NIST SSDF practices
/gdit-sdaf-gitlab-security-scanning - Run security scans (works for GitHub too)
```

**Web UI (optional):**

Start the web dashboard:

```bash
# From source
bun run dev

# From binary
archon serve
```

Navigate to `http://localhost:3090` to:
- Chat with your AI coding agent
- Monitor running workflows in real-time
- View workflow history and metrics
- Create/edit workflows visually
- Register and manage projects

### Configuration Files

**~/.archon/.env** - Credentials (never commit this)
```bash
# AI Assistants
CLAUDE_USE_GLOBAL_AUTH=true
DEFAULT_AI_ASSISTANT=claude

# Forge (GitHub or GitLab)
FORGE_PROVIDER=github          # or gitlab
GITHUB_TOKEN=ghp_xxxxx         # or GITLAB_TOKEN=glpat_xxxxx
GH_TOKEN=ghp_xxxxx             # same as GITHUB_TOKEN
WEBHOOK_SECRET=xxxxx           # auto-generated
GITHUB_ALLOWED_USERS=user1,user2   # optional whitelist

# Optional platforms
TELEGRAM_BOT_TOKEN=xxxxx
SLACK_BOT_TOKEN=xxxxx
```

**~/.archon/config.yaml** - Preferences
```yaml
assistants:
  claude:
    model: sonnet
    settingSources: [project, user]
    claudeBinaryPath: /path/to/claude  # if using compiled binary

forge:
  provider: github  # or gitlab (set by meridian-onboard)

worktree:
  baseBranch: dev   # or main, master

docs:
  path: docs/       # documentation directory
```

**<project>/.archon/config.yaml** - Project-specific overrides
```yaml
forge:
  provider: gitlab  # override for this project only

worktree:
  baseBranch: main

docs:
  path: documentation/
```

### Troubleshooting Setup

**"CLAUDE_BIN_PATH is not set"** (compiled binaries only)
```bash
# Find where Claude Code installed
which claude

# Set the path (add to ~/.bashrc or ~/.zshrc)
export CLAUDE_BIN_PATH="$HOME/.local/bin/claude"

# Or set in config
echo "assistants:
  claude:
    claudeBinaryPath: $HOME/.local/bin/claude" > ~/.archon/config.yaml
```

**"gh auth status failed"** or **"glab auth status failed"**
```bash
# GitHub
gh auth login

# GitLab
glab auth login
```

**"Python version too old"**
```bash
# Install Python 3.12+
# macOS
brew install python@3.12

# Ubuntu/Debian
sudo apt install python3.12

# Windows - download from python.org
```

**"Database not reachable"**

By default, Archon uses SQLite at `~/.archon/archon.db` (auto-created, no setup needed). If you see database errors, check disk space and permissions.

For PostgreSQL (optional, for heavy workloads):
```bash
docker compose --profile with-db up -d postgres
echo "DATABASE_URL=postgresql://postgres:postgres@localhost:5432/remote_coding_agent" >> ~/.archon/.env
```

### Next Steps

- [Core Concepts](https://archon.diy/getting-started/concepts/) - Understand workflows, nodes, commands
- [Authoring Workflows](https://archon.diy/guides/authoring-workflows/) - Create custom YAML workflows
- [GDIT-SDAF Skills](/GDIT_ONBOARDING.md) - Deep dive into security and compliance features
- [CLI Reference](https://archon.diy/reference/cli/) - Full command reference

## Why Archon?

When you ask an AI agent to "fix this bug", what happens depends on the model's mood. It might skip planning. It might forget to run tests. It might write a PR description that ignores your template. Every run is different.

Archon fixes this. Encode your development process as a workflow. The workflow defines the phases, validation gates, and artifacts. The AI fills in the intelligence at each step, but the structure is deterministic and owned by you.

- **Repeatable** - Same workflow, same sequence, every time. Plan, implement, validate, review, PR.
- **Isolated** - Every workflow run gets its own git worktree. Run 5 fixes in parallel with no conflicts.
- **Fire and forget** - Kick off a workflow, go do other work. Come back to a finished PR with review comments.
- **Composable** - Mix deterministic nodes (bash scripts, tests, git ops) with AI nodes (planning, code generation, review). The AI only runs where it adds value.
- **Portable** - Define workflows once in `.archon/workflows/`, commit them to your repo. They work the same from CLI, Web UI, Slack, Telegram, or GitHub.

## What It Looks Like

Here's an example of an Archon workflow that plans, implements in a loop until tests pass, gets your approval, then creates the PR:

```yaml
# .archon/workflows/build-feature.yaml
nodes:
  - id: plan
    prompt: "Explore the codebase and create an implementation plan"

  - id: implement
    depends_on: [plan]
    loop:                                      # AI loop - iterate until done
      prompt: "Read the plan. Implement the next task. Run validation."
      until: ALL_TASKS_COMPLETE
      fresh_context: true                      # Fresh session each iteration

  - id: run-tests
    depends_on: [implement]
    bash: "bun run validate"                   # Deterministic - no AI

  - id: review
    depends_on: [run-tests]
    prompt: "Review all changes against the plan. Fix any issues."

  - id: approve
    depends_on: [review]
    loop:                                      # Human approval gate
      prompt: "Present the changes for review. Address any feedback."
      until: APPROVED
      interactive: true                        # Pauses and waits for human input

  - id: create-pr
    depends_on: [approve]
    prompt: "Push changes and create a pull request"
```

Tell your coding agent what you want, and Archon handles the rest:

```
You: Use archon to fix issue #42

Agent: I'll run the gdit-sdaf-fix-github-issue workflow for this.
       → Creating isolated worktree on branch archon/issue-42...
       → Planning implementation...
       → Running security scans (bandit, safety, semgrep)...
       → Implementing fix...
       → Validating against SSDF requirements...
       → All scans passed, creating PR...
       → PR ready: https://github.com/you/project/pull/47
```

## Previous Version

Looking for the original Python-based Archon (task management + RAG)? It's fully preserved on the [`archive/v1-task-management-rag`](https://github.com/coleam00/Archon/tree/archive/v1-task-management-rag) branch.

## Web UI

Archon includes a web dashboard for chatting with your AI coding agent, running workflows, and monitoring activity. 

**Starting the Web UI:**

```bash
# From source (recommended)
bun run dev

# From binary
archon serve
```

Navigate to `http://localhost:3090`

**Key features:**
- **Chat** - Conversation interface with real-time streaming and tool call visualization
- **Dashboard** - Mission Control for monitoring running workflows, with filterable history by project, status, and date
- **Workflow Builder** - Visual drag-and-drop editor for creating DAG workflows with loop nodes
- **Workflow Execution** - Step-by-step progress view for any running or completed workflow

**Monitoring hub:** The sidebar shows conversations from **all platforms** - not just the web. Workflows kicked off from the CLI, messages from Slack or Telegram, GitHub/GitLab issue interactions - everything appears in one place.

Register a project by clicking **+** next to "Project" in the chat sidebar - enter a GitHub/GitLab URL or local path. Then start a conversation, invoke workflows, and watch progress in real time.

See the [Web UI Guide](https://archon.diy/adapters/web/) for full documentation.

## What Can You Automate?

Archon + GDIT-SDAF ships with 18 base workflows plus 10+ GDIT-specific workflows for secure development:

### Base Archon Workflows

| Workflow | What it does |
|----------|-------------|
| `archon-assist` | General Q&A, debugging, exploration - full Claude Code agent with all tools |
| `archon-fix-github-issue` | Classify issue → investigate/plan → implement → validate → PR → smart review → self-fix |
| `archon-idea-to-pr` | Feature idea → plan → implement → validate → PR → 5 parallel reviews → self-fix |
| `archon-plan-to-pr` | Execute existing plan → implement → validate → PR → review → self-fix |
| `archon-issue-review-full` | Comprehensive fix + full multi-agent review pipeline for issues |
| `archon-smart-pr-review` | Classify PR complexity → run targeted review agents → synthesize findings |
| `archon-comprehensive-pr-review` | Multi-agent PR review (5 parallel reviewers) with automatic fixes |
| `archon-create-issue` | Classify problem → gather context → investigate → create issue |
| `archon-validate-pr` | Thorough PR validation testing both main and feature branches |
| `archon-resolve-conflicts` | Detect merge conflicts → analyze both sides → resolve → validate → commit |
| `archon-feature-development` | Implement feature from plan → validate → create PR |
| `archon-architect` | Architectural sweep, complexity reduction, codebase health improvement |
| `archon-refactor-safely` | Safe refactoring with type-check hooks and behavior verification |

### GDIT-SDAF Workflows

| Workflow | What it does |
|----------|-------------|
| `meridian-onboard` | One-command setup: forge detection, CLI auth, scanner install, config generation |
| `gdit-sdaf-fix-issue` | Secure issue resolution with security scans at every gate |
| `gdit-sdaf-secure-pr` | Create PR with mandatory security validation and compliance checks |
| `gdit-sdaf-audit` | Run full security audit (bandit, safety, semgrep, trivy) and generate report |
| `gdit-sdaf-ssdf-attest` | Generate NIST SSDF attestation with evidence for completed work |

**List all available workflows:**
```bash
archon workflow list
```

**Or just describe what you want** - the router picks the right workflow automatically.

**Define your own workflows.** Workflows are YAML files in `.archon/workflows/`, commands are markdown files in `.archon/commands/`. Same-named files in your repo override the bundled defaults. Commit them - your whole team runs the same process.

See [Authoring Workflows](https://archon.diy/guides/authoring-workflows/) and [Authoring Commands](https://archon.diy/guides/authoring-commands/).

## Workflow Metrics

Every workflow run automatically produces a `metrics.json` file in its artifacts directory (`~/.archon/workspaces/<owner>/<repo>/artifacts/runs/<run-id>/`). The file captures:

- **Timing** — wall-clock duration in milliseconds
- **Outcome** — success, failure, or abandonment
- **Execution stats** — nodes defined/executed/skipped, loop iterations, retries
- **Cost** — tokens in/out/cache-read/cache-write and USD per node
- **Human gates** — approval count, wait times, rejections
- **Quality outcomes** — validation pass/fail, code review findings by severity
- **Codebase fingerprint** — repo, commit SHA, working path

**Input signals** are captured automatically at workflow start:
- `size_proxy_emitted` — input word count plus git diff stats (additions, deletions, changed files)
- `classifier_emitted` — fires when any node's structured output contains classifier fields (`issue_type`, `area`, `scope`, `confidence`)

**Monthly rollup:** every completed run also appends a summary line to `~/.archon/metrics/runs-YYYY-MM.jsonl`. Query your history with `jq`:

```bash
# Average cost per workflow this month
jq -s '[.[] | select(.workflow == "archon-fix-github-issue")] | { avg_usd: (map(.cost_usd) | add / length) }' \
  ~/.archon/metrics/runs-$(date +%Y-%m).jsonl

# All failed runs
jq 'select(.outcome == "failure")' ~/.archon/metrics/runs-$(date +%Y-%m).jsonl
```

**PR reconciliation:** the `archon-metrics-reconcile` workflow runs daily, finds `metrics.json` files with unresolved `outcome_followup` fields (null `pr_merged`, `ci_passed`), queries GitHub for current PR status, and writes the results back. Run it on demand with `archon workflow run archon-metrics-reconcile`.

The metrics data is designed to accumulate over time so you can build effort estimation models - correlating input size and complexity signals with actual cost and cycle time across your real workload.

## Platform Integrations

The Web UI and CLI work out of the box. Optionally connect platforms for remote access and automation:

### Forge Adapters (for issue/PR automation)

| Platform | Setup time | Guide |
|----------|-----------|-------|
| **GitHub** | 15 min | [GitHub Guide](https://archon.diy/adapters/github/) - Configured during `archon setup` |
| **GitLab** | 15 min | [GitLab Guide](https://archon.diy/adapters/gitlab/) - Configured during `archon setup` |

### Chat Adapters (for remote access)

| Platform | Setup time | Guide |
|----------|-----------|-------|
| **Telegram** | 5 min | [Telegram Guide](https://archon.diy/adapters/telegram/) |
| **Slack** | 15 min | [Slack Guide](https://archon.diy/adapters/slack/) |
| **Discord** | 5 min | [Discord Guide](https://archon.diy/adapters/community/discord/) |

All platforms are configured through `archon setup` or by manually editing `~/.archon/.env`.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│  Platform Adapters (Web UI, CLI, Telegram, Slack,       │
│                    Discord, GitHub)                     │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                     Orchestrator                        │
│          (Message Routing & Context Management)         │
└─────────────┬───────────────────────────┬───────────────┘
              │                           │
      ┌───────┴────────┐          ┌───────┴────────┐
      │                │          │                │
      ▼                ▼          ▼                ▼
┌───────────┐  ┌────────────┐  ┌──────────────────────────┐
│  Command  │  │  Workflow  │  │    AI Assistant Clients  │
│  Handler  │  │  Executor  │  │   (Claude / Codex / Pi)  │
│  (Slash)  │  │  (YAML)    │  │                          │
└───────────┘  └────────────┘  └──────────────────────────┘
      │              │                      │
      └──────────────┴──────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│              SQLite / PostgreSQL (7 Tables)             │
│   Codebases • Conversations • Sessions • Workflow Runs  │
│    Isolation Environments • Messages • Workflow Events  │
└─────────────────────────────────────────────────────────┘
```

## Documentation

Full documentation is available at **[archon.diy](https://archon.diy)**.

| Topic | Description |
|-------|-------------|
| [Setup & Installation](#setup--installation) | Complete setup guide above (with GDIT-SDAF) |
| [GDIT-SDAF Onboarding](/GDIT_ONBOARDING.md) | Deep dive into security workflows and skills |
| [Getting Started](https://archon.diy/getting-started/overview/) | Base Archon setup guide |
| [The Book of Archon](https://archon.diy/book/) | 10-chapter narrative tutorial |
| [CLI Reference](https://archon.diy/reference/cli/) | Full CLI reference |
| [Authoring Workflows](https://archon.diy/guides/authoring-workflows/) | Create custom YAML workflows |
| [Authoring Commands](https://archon.diy/guides/authoring-commands/) | Create reusable AI commands |
| [Configuration](https://archon.diy/reference/configuration/) | All config options, env vars, YAML settings |
| [AI Assistants](https://archon.diy/getting-started/ai-assistants/) | Claude, Codex, and Pi setup details |
| [Deployment](https://archon.diy/deployment/) | Docker, VPS, production setup |
| [Architecture](https://archon.diy/reference/architecture/) | System design and internals |
| [Troubleshooting](https://archon.diy/reference/troubleshooting/) | Common issues and fixes |

## Telemetry

Archon sends a single anonymous event — `workflow_invoked` — each time a workflow starts, so maintainers can see which workflows get real usage and prioritize accordingly. **No PII, ever.**

**What's collected:** the workflow name, the workflow description (both authored by you in YAML), the platform that triggered it (`cli`, `web`, `slack`, etc.), the Archon version, and a random install UUID stored at `~/.archon/telemetry-id`. Nothing else.

**What's *not* collected:** your code, prompts, messages, git remotes, file paths, usernames, tokens, AI output, workflow node details — none of it.

**Opt out:** set any of these in your environment:

```bash
ARCHON_TELEMETRY_DISABLED=1
DO_NOT_TRACK=1        # de facto standard honored by Astro, Bun, Prisma, Nuxt, etc.
```

Self-host PostHog or use a different project by setting `POSTHOG_API_KEY` and `POSTHOG_HOST`.

## Contributing

Contributions welcome! See the open [issues](https://github.com/coleam00/Archon/issues) for things to work on.

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting a pull request.

## Star History

[![Star History Chart](https://api.star-history.com/chart?repos=coleam00/Archon&type=date&legend=top-left)](https://www.star-history.com/?repos=coleam00%2FArchon&type=date&legend=top-left)

## License

[MIT](LICENSE)
