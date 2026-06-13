---
description: Review validation output and fix any type check, lint, test, or format failures
argument-hint: (none - reads $validate.output from workflow context)
---

# Fix Validation Failures

## Validation Output

$validate.output

## Instructions

If the output ends with "VALIDATION_STATUS: PASS", respond with
"All checks passed — no fixes needed." and stop.

If there are failures:

1. Read the validation failures carefully
2. Fix ONLY what's broken — do not make additional improvements
3. If a fix requires changing behavior (not just fixing a type/lint error),
   revert the original change instead
4. Run the specific failing check after each fix to confirm it passes
5. After all fixes, run the full validation suite: `bun run validate`
