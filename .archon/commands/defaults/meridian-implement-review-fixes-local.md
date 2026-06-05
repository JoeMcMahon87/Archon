---
description: Implement CRITICAL and HIGH fixes from review, add tests, report remaining issues (local-only, no GitHub/GitLab required)
argument-hint: (none - reads from consolidated review artifact)
---

# Implement Review Fixes (Local)

---

## IMPORTANT: Output Behavior

**Your output will be appended to the fix report artifact.** Keep your working output minimal:
- Do NOT narrate each step ("Now I'll read the file...", "Let me check...")
- Do NOT output verbose progress updates
- Only output the final structured report at the end
- Use the TodoWrite tool to track progress silently

---

## Your Mission

Read the consolidated review artifact and implement all CRITICAL and HIGH priority fixes. Add tests for fixed code if missing. Commit changes locally. Report what was fixed, what wasn't (and why), and suggest follow-up issues for remaining items.

**No PR or forge connection is required.** The fix report is written to disk; the user pushes and posts it manually.

**Output artifact**: `$ARTIFACTS_DIR/review/fix-report.md`
**Git action**: Commit fixes locally (push manually afterward)

---

## Phase 1: LOAD - Get Fix List

### 1.1 Identify Current Branch

```bash
# Derive current branch from git directly — no forge API needed
HEAD_BRANCH=$(git branch --show-current)
echo "Branch: $HEAD_BRANCH"
```

### 1.2 Verify Git State

```bash
git status --porcelain
```

Confirm you are on the expected feature branch (not base branch). If on the base branch, stop with:

```
❌ Currently on base branch ($HEAD_BRANCH). Switch to the feature branch first.
```

### 1.3 Read Consolidated Review

```bash
cat $ARTIFACTS_DIR/review/consolidated-review.md
```

Extract:
- All CRITICAL issues with fixes
- All HIGH issues with fixes
- MEDIUM issues (for reporting)
- LOW issues (for reporting)

### 1.4 Read Individual Artifacts for Details

If consolidated doesn't have full fix code, read original artifacts:

```bash
cat $ARTIFACTS_DIR/review/code-review-findings.md
cat $ARTIFACTS_DIR/review/error-handling-findings.md
cat $ARTIFACTS_DIR/review/test-coverage-findings.md
cat $ARTIFACTS_DIR/review/docs-impact-findings.md
```

**PHASE_1_CHECKPOINT:**
- [ ] Current branch identified via git
- [ ] On feature branch (NOT base branch)
- [ ] Consolidated review loaded
- [ ] CRITICAL/HIGH issues extracted

---

## Phase 2: IMPLEMENT - Apply Fixes

### 2.1 For Each CRITICAL Issue

1. **Read the file**
2. **Apply the recommended fix**
3. **Verify fix compiles**: `bun run type-check`
4. **Track**: Note what was changed

### 2.2 For Each HIGH Issue

Same process as CRITICAL.

### 2.3 For Test Coverage Gaps

If test-coverage-agent identified missing tests for fixed code:

1. **Create/update test file**
2. **Add tests for the fix**
3. **Verify tests pass**: `bun test {file}`

### 2.4 Handle Unfixable Issues

If a fix cannot be applied:
- **Conflict**: Code has changed since review
- **Complex**: Requires architectural changes
- **Unclear**: Recommendation is ambiguous
- **Risk**: Fix might break other things

Document the reason clearly.

**PHASE_2_CHECKPOINT:**
- [ ] All CRITICAL fixes attempted
- [ ] All HIGH fixes attempted
- [ ] Tests added for fixes
- [ ] Unfixable issues documented

---

## Phase 3: VALIDATE - Verify Fixes

### 3.1 Type Check

```bash
bun run type-check
```

Must pass. If not, fix type errors.

### 3.2 Lint

```bash
bun run lint
```

Fix any lint errors introduced.

### 3.3 Run Tests

```bash
bun test
```

All tests must pass. If new tests fail, fix them.

### 3.4 Build Check

```bash
bun run build
```

Must succeed.

**PHASE_3_CHECKPOINT:**
- [ ] Type check passes
- [ ] Lint passes
- [ ] All tests pass
- [ ] Build succeeds

---

## Phase 4: COMMIT - Save Changes Locally

### 4.1 Stage Changes

Stage **only** the files you actually edited while applying review fixes — never `git add -A`, `git add .`, or `git add -u`. List them by name:

```bash
git add path/to/file1 path/to/file2 ...
git status --porcelain  # verify nothing scratch/review/PR-body is staged
```

**Never stage**:

- `.pr-body.md`, `pr-body.md`, `*.scratch.md`, `*.tmp.md`
- `review/`, `*-report.md` at the repo root
- Anything under `$ARTIFACTS_DIR` (review artifacts live here, not in the worktree)

### 4.2 Commit

```bash
git commit -m "fix: Address review findings (CRITICAL/HIGH)

Fixes applied:
- {brief list of fixes}

Tests added:
- {list of new tests if any}

Skipped (see review artifacts):
- {brief list of unfixable if any}

Review artifacts: $ARTIFACTS_DIR/review/"
```

**PHASE_4_CHECKPOINT:**
- [ ] Changes committed locally
- [ ] Commit message lists fixes

---

## Phase 5: GENERATE - Create Fix Report

Write to `$ARTIFACTS_DIR/review/fix-report.md`:

```markdown
# Fix Report

**Date**: {ISO timestamp}
**Status**: {COMPLETE | PARTIAL}
**Branch**: {HEAD_BRANCH}

---

## Summary

{2-3 sentence overview of fixes applied}

---

## Fixes Applied

### CRITICAL Fixes ({n}/{total})

| Issue | Location | Status | Details |
|-------|----------|--------|---------|
| {title} | `file:line` | ✅ FIXED | {what was done} |
| {title} | `file:line` | ❌ SKIPPED | {why} |

---

### HIGH Fixes ({n}/{total})

| Issue | Location | Status | Details |
|-------|----------|--------|---------|
| {title} | `file:line` | ✅ FIXED | {what was done} |

---

## Tests Added

| Test File | Test Cases | For Issue |
|-----------|------------|-----------|
| `src/x.test.ts` | `it('should...')` | {issue title} |

---

## Not Fixed (Requires Manual Action)

### {Issue Title}

**Severity**: {CRITICAL/HIGH}
**Location**: `{file}:{line}`
**Reason Not Fixed**: {reason}

**Suggested Action**:
{What the user should do}

---

## MEDIUM Issues (User Decision Required)

| Issue | Location | Options |
|-------|----------|---------|
| {title} | `file:line` | Fix now / Create issue / Skip |

---

## LOW Issues (For Consideration)

| Issue | Location | Suggestion |
|-------|----------|------------|
| {title} | `file:line` | {brief suggestion} |

---

## Suggested Follow-up Issues

| Issue Title | Priority | Related Finding |
|-------------|----------|-----------------|
| "{title}" | P{1/2/3} | {which finding} |

---

## Validation Results

| Check | Status |
|-------|--------|
| Type check | ✅ |
| Lint | ✅ |
| Tests | ✅ ({n} passed) |
| Build | ✅ |

---

## Git Status

- **Branch**: {HEAD_BRANCH}
- **Commit**: {commit-hash}
- **Pushed**: ⏳ Push manually: `git push origin {HEAD_BRANCH}`
```

**PHASE_5_CHECKPOINT:**
- [ ] Fix report created
- [ ] All fixes documented

---

## Phase 6: NOTIFY - Print Fix Report Location

```bash
echo ""
echo "✅ Fix implementation complete."
echo ""
echo "Fix report written to:"
echo "  $ARTIFACTS_DIR/review/fix-report.md"
echo ""
echo "To push your changes and open a PR:"
echo "  git push origin $HEAD_BRANCH"
echo "  gh pr create --base \$BASE_BRANCH --body-file $ARTIFACTS_DIR/.pr-body.md"
echo "  glab mr create --target-branch \$BASE_BRANCH --description \"\$(cat $ARTIFACTS_DIR/.pr-body.md)\""
echo ""
echo "To attach the fix report to an existing PR:"
echo "  gh pr comment --body-file $ARTIFACTS_DIR/review/fix-report.md"
echo "  glab mr note --message \"\$(cat $ARTIFACTS_DIR/review/fix-report.md)\""
echo ""
```

**PHASE_6_CHECKPOINT:**
- [ ] Artifact paths printed
- [ ] Manual push/PR instructions shown

---

## Phase 7: OUTPUT - Final Report

Output only this summary (keep it brief):

```markdown
## ✅ Fix Implementation Complete

**Branch**: {HEAD_BRANCH}
**Status**: {COMPLETE | PARTIAL}

| Severity | Fixed |
|----------|-------|
| CRITICAL | {n}/{total} |
| HIGH | {n}/{total} |

**Validation**: ✅ All checks pass
**Committed**: ✅ Changes committed locally

Push when ready: `git push origin {HEAD_BRANCH}`

See fix report: `$ARTIFACTS_DIR/review/fix-report.md`
```

---

## Error Handling

### Type Check Fails After Fix

1. Review the error
2. Adjust the fix
3. Re-run type check
4. If still failing, mark as "Not Fixed" with reason

### Tests Fail

1. Check if fix caused the failure
2. Either: fix the implementation, or fix the test
3. If unclear, mark as "Not Fixed" for manual review

---

## Success Criteria

- **ON_FEATURE_BRANCH**: Working on the feature branch, not base branch
- **CRITICAL_ADDRESSED**: All CRITICAL issues attempted
- **HIGH_ADDRESSED**: All HIGH issues attempted
- **VALIDATION_PASSED**: Type check, lint, tests, build all pass
- **COMMITTED_LOCALLY**: Changes committed locally (push is manual)
- **REPORTED**: Fix report artifact written to `$ARTIFACTS_DIR/review/fix-report.md`
- **NO_NETWORK**: No forge API calls made; safe for fully offline use
