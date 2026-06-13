---
description: Verify that the current PR/MR targets the correct base branch and re-target if needed
argument-hint: (none - uses $BASE_BRANCH and $FORGE_PROVIDER)
---

# Verify PR Base Branch

Ensure the open PR or MR for the current branch is targeting `$BASE_BRANCH`. Re-target it automatically if not.

---

## Step 1: Get Current Branch

```bash
git branch --show-current
```

---

## Step 2: Check PR/MR Base Branch

### If `$FORGE_PROVIDER` is `gitlab`:

```bash
BRANCH=$(git branch --show-current)
glab mr list --source-branch "$BRANCH" --output json --limit 1
```

Extract `target_branch` and `iid` from the first result.

- If the result is empty or `target_branch` is absent: report `MR base verified: $BASE_BRANCH` and stop.
- If `target_branch` equals `$BASE_BRANCH`: report `MR base verified: $BASE_BRANCH` and stop.
- If `target_branch` differs: proceed to Step 3 (GitLab path).

### If `$FORGE_PROVIDER` is `github` (or unset):

```bash
gh pr view --json baseRefName -q '.baseRefName'
```

- If the command fails or returns empty (no open PR): report `PR base verified: $BASE_BRANCH` and stop.
- If the result equals `$BASE_BRANCH`: report `PR base verified: $BASE_BRANCH` and stop.
- If the result differs: proceed to Step 3 (GitHub path).

---

## Step 3: Fix Base Branch Mismatch

### GitLab:

```bash
glab mr update <MR_IID> --target-branch $BASE_BRANCH
```

Report:
```
Base mismatch on MR !<IID>: expected=$BASE_BRANCH actual=<actual> — re-targeting
```

### GitHub:

```bash
PR_NUMBER=$(gh pr view --json number -q '.number')
gh pr edit "$PR_NUMBER" --base $BASE_BRANCH
```

Report:
```
Base mismatch on PR #<number>: expected=$BASE_BRANCH actual=<actual> — re-targeting
```

---

## Success Criteria

- **VERIFIED**: The PR/MR targets `$BASE_BRANCH` (either already correct or corrected in this step).
