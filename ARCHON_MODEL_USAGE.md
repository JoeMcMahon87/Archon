# Archon Model Usage Chart

Which agents, skills, commands, and workflows in this repo use which AI model.

## How models are assigned in Archon

Model resolution differs by artifact type — only two of the four actually pin a model:

| Artifact type | Declares a model? | Where the model comes from |
|---|---|---|
| **Agents** (`.claude/agents/`) | ✅ Yes — `model:` in frontmatter | Own frontmatter (or `inherit`) |
| **Workflows** (`.archon/workflows/`) | ✅ Yes — `provider:`/`model:` at workflow + per-node | Node override → workflow default → config default |
| **Commands** (`.archon/commands/`, `.claude/commands/`) | ❌ No — plain prompt files | Set by the workflow `command:` node that invokes them |
| **Skills** (`.claude/skills/`) | ❌ No — no `model:` in any SKILL.md | Inherit the invoker's model (Claude Code session or workflow node) |

All **78 commands** (59 `.archon` + 19 `.claude`) and all **29 skills** carry **no model** — they inherit from whatever invokes them. So the meaningful model chart is Agents + Workflows.

## Agents (16) — model from frontmatter

| Model | Agents |
|---|---|
| **sonnet** (13) | code-reviewer, code-simplifier, codebase-analyst, codebase-explorer, comment-analyzer, docs-impact, pr-test-analyzer, rulecheck-agent, sdk-verifier, silent-failure-hunter, triage-agent, type-design-analyzer, web-researcher |
| **inherit** (3) | gdit-sdaf-dotnet, gdit-sdaf-java, gdit-sdaf-plan |

## Workflows (55) — grouped by provider

### Claude provider

Default model shown; per-node overrides in the right column. `[1m]` = 1M-context beta.

| Workflow | Default model | Node overrides |
|---|---|---|
| meridian-validate-pr | opus | haiku |
| meridian-adversarial-dev | sonnet | haiku, opus[1m] |
| meridian-fix-github-issue | sonnet | haiku×2, opus[1m] |
| meridian-idea-to-pr-sdaf | sonnet | opus |
| meridian-idea-to-validated-sdaf | sonnet | opus |
| meridian-plan-to-pr-local | sonnet | opus |
| meridian-plan-to-pr-sdaf | sonnet | opus |
| maintainer-standup | sonnet | — |
| experimental/archon-fix-github-issue-experimental | sonnet | haiku×2, opus[1m] |
| experimental/archon-release | sonnet | haiku×2 |
| meridian-interactive-prd | inherit | sonnet×5 |
| meridian-interactive-prd-local | inherit | sonnet×5 |
| meridian-piv-loop | inherit | sonnet×3, claude-opus-4-6[1m] |
| meridian-ralph-dag | inherit | haiku, opus[1m] |
| meridian-refactor-safely | inherit | opus[1m] |
| meridian-architect | inherit | — |
| marketplace-pr-review-and-merge | claude-haiku-4-5-20251001 | — |
| e2e-claude-smoke | haiku | — |
| e2e-mixed-providers | haiku | codex/gpt-5.2 (mixed) |

### Pi provider (one harness, many backends)

| Workflow | Model |
|---|---|
| maintainer-review-pr | minimax/MiniMax-M2.7 |
| maintainer-standup-minimax | minimax/MiniMax-M2.7 |
| repo-triage-minimax | minimax/MiniMax-M2.7 (6 nodes) |
| e2e-minimax-smoke | minimax/MiniMax-M2.7 |
| e2e-pi-smoke | anthropic/claude-haiku-4-5 |
| e2e-pi-all-nodes-smoke | anthropic/claude-haiku-4-5 |
| archon-test-pi | kiro/minimax-m2-5 (+ node ollama/qwen3.5) |

### Other providers

| Workflow | Provider / Model |
|---|---|
| e2e-codex-smoke | codex / gpt-5.2 |
| e2e-copilot-abort | copilot / gpt-5-mini |
| e2e-copilot-all-nodes-smoke | copilot / gpt-5-mini |
| e2e-opencode-all-nodes-smoke | opencode / opencode/big-pickle |
| e2e-opencode-inline-multi-agents | opencode / opencode/big-pickle |
| e2e-opencode-smoke | opencode / inherit |

### Fully inherited (no provider/model set — run the config default)

No per-node model pins:

- meridian-assist
- meridian-assist-local
- meridian-compliance-report
- meridian-comprehensive-pr-review
- meridian-issue-review-full
- meridian-metrics-reconcile
- meridian-onboard
- meridian-resolve-conflicts
- meridian-security-scan
- meridian-setup
- meridian-test-loop-dag
- meridian-idea-to-pr
- meridian-idea-to-validated
- meridian-plan-to-pr
- e2e-deterministic
- e2e-worktree-disabled

Inherit at the top level but override individual nodes:

- meridian-create-issue (haiku)
- meridian-estimate-effort (haiku)
- meridian-remotion-generate (haiku)
- meridian-smart-pr-review (haiku)
- meridian-workflow-builder (haiku)
- meridian-feature-development (claude/opus[1m])
- repo-triage (haiku×4, sonnet×6)

## Takeaways

- **Sonnet is the workhorse** for both agents (13/16) and the main Claude workflows; **haiku** is used for cheap deterministic/glue nodes; **opus (often `[1m]`)** is reserved for heavy reasoning nodes (planning, adversarial review, validation).
- **Production maintainer workflows** deliberately run **Pi + MiniMax-M2.7** (cost/independence), with Claude-based variants kept alongside.
- **`test-workflows/` and `e2e-*`** exist to smoke-test each provider (claude, codex, copilot, pi, opencode, minimax) and pin small/cheap models.
- **Commands and skills never pin a model** — they're prompt/instruction bundles that inherit the model of the workflow node or session that calls them.
