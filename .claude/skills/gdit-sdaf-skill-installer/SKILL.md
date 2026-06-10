---
name: skill-installer
description: Install skills from GitHub or local paths into ~/.specs/skills. Use when a user asks to list installable skills, install a skill from a GitHub registry, or install from a local directory. Note: GitLab is not yet implemented.
metadata:
  short-description: Install skills from GitHub or local paths (GitLab not yet implemented)
---

# Skill Installer

Install skills from multiple sources into `~/.specs/skills/`.

> **Offline / air-gapped use**: Use `install-skill-from-local.py` with a pre-downloaded skill directory — it has no network dependencies at all.
>
> **GitLab**: Not yet implemented. `install-skill-from-github.py` only supports `github.com` and public GitHub Enterprise mirrors via the `GITHUB_API_BASE_URL` env var.

## Supported Sources

| Source | Script | Example | Network required? |
|--------|--------|---------|-------------------|
| GitHub repo | `install-skill-from-github.py` | `--repo org/repo --path skills/my-skill` | Yes |
| GitHub URL | `install-skill-from-github.py` | `--url https://github.com/org/repo/tree/main/skills/my-skill` | Yes |
| Local path | `install-skill-from-local.py` | `./path/to/my-skill` | No |

## Usage

**List available skills from a GitHub registry:**
```
python3 ~/.specs/skills/skill-installer/scripts/list-skills.py --repo org/skills-catalog
```

**Install from GitHub (repo + path):**
```
python3 ~/.specs/skills/skill-installer/scripts/install-skill-from-github.py --repo org/repo --path skills/my-skill
```

**Install from GitHub URL:**
```
python3 ~/.specs/skills/skill-installer/scripts/install-skill-from-github.py --url https://github.com/org/repo/tree/main/skills/my-skill
```

**Install from local directory:**
```
python3 ~/.specs/skills/skill-installer/scripts/install-skill-from-local.py ./path/to/my-skill
```

After installing, restart the agent to pick up new skills.

## Communication

When listing skills, output:
```
Skills from registry:
1. skill-1
2. skill-2 (already installed)
Which ones would you like installed?
```

After installing: "Installed [name]. Restart the agent to pick up new skills."

## Prerequisites

This skill requires GDIT-SDAF to be set up. Run once per machine:

```
archon workflow run gdit-sdaf-setup
```

After setup, scripts are available at `~/.specs/skills/skill-installer/scripts/`.
