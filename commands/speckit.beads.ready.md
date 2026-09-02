---
description: "Select ready Beads work and restore its linked Spec Kit context"
---

## User Input

```text
$ARGUMENTS
```

Choose ready implementation work from Beads while preserving the Spec Kit design context. Beads—not `tasks.md`—is authoritative.

1. Verify the repository contains `.specify/` and `.beads/`.
2. Resolve the current feature and run `bd ready --json`. Keep tasks labeled both `speckit` and `speckit:<feature-directory-name>`. If the user supplied a Beads ID, inspect that issue instead.
3. If no matching task is ready, show the relevant blocking dependency tree and stop.
4. Select the highest-priority ready task. When priorities tie, prefer the lowest `metadata.speckit.display_id`. If the choice is genuinely ambiguous, show the small candidate set and ask the user.
5. Run `bd show <id> --json`. Read `metadata.speckit.source_refs` and the feature artifact map from the parent epic.
6. Load the cited sections plus the feature's `spec.md` and `plan.md`. Read research, data-model, or contract files only when cited by the task. Do not read unrelated feature directories.
7. If the user included `claim`, atomically claim it with `bd update <id> --claim`. Otherwise leave issue state unchanged.
8. Return a compact implementation brief: Beads ID, display ID, task text, phase/story, dependencies, acceptance criteria, relevant sources, and whether it was claimed.

Beads is authoritative for task identity, dependencies, and execution state. Spec Kit files remain authoritative for product intent and implementation design.

