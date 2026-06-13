---
description: Detect whether $ARGUMENTS is a GDIT spec directory, an Archon plan.md, or unknown
argument-hint: (none - reads $ARGUMENTS and checks $ARTIFACTS_DIR/plan.md as fallback)
---

Determine the input type from: $ARGUMENTS

Rules:
- If $ARGUMENTS points to a directory containing tasks.md → output exactly: gdit-spec
- If $ARGUMENTS points to a .md file containing "## Tasks" or "## Step-by-Step Tasks" → output exactly: archon-plan
- If $ARGUMENTS is empty → check $ARTIFACTS_DIR/plan.md; if found, output: archon-plan
- If nothing matches → output: unknown

Output ONLY one of: gdit-spec OR archon-plan OR unknown
