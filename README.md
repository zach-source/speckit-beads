# Spec Kit Beads

Use Beads as Spec Kit's task-planning and execution backend.

The extension replaces the normal `tasks.md`-first handoff:

```text
specify → plan → beads tasks → bd ready/claim/close
```

Spec Kit remains authoritative for requirements and design. Beads becomes authoritative for task identity, dependencies, priority, assignment, and status.

## Commands

- `speckit.beads.tasks` reads `spec.md`, `plan.md`, and available research, data-model, contract, quickstart, and constitution artifacts. It creates or reconciles a dependency-ordered Beads epic and task graph. The former `speckit.beads.sync` name remains an alias.
- `speckit.beads.ready` selects dependency-ready work and restores the exact cited Spec Kit context.
- `speckit.beads.implement` implements through the Beads lifecycle: ready, claim, validate, and close.
- `speckit.beads.render` optionally generates `tasks.md` from Beads for core commands such as `speckit.analyze` that still require it.

The optional `after_plan` hook offers to create the Beads graph immediately after technical planning. An optional `before_analyze` hook renders a fresh compatibility view for the core analyzer.

## Reconciliation model

The agent creates a short-lived JSON reconciliation input conforming to [schemas/task-graph.schema.json](schemas/task-graph.schema.json). The deterministic helper validates the graph and applies it to Beads.

Stable identities prevent churn:

- Epic: `speckit:<feature-key>`
- Task: `speckit:<feature-key>:<semantic-task-key>`
- Display ID: `T001`, `T002`, and so on, preserved in Beads metadata

Reconciliation updates planning fields while preserving execution status, human labels, non-Spec-Kit metadata, and external dependencies. Missing tasks are reported as stale; they are never automatically closed or deleted.

Each task stores `metadata.speckit.source_refs`, linking it to the relevant specification, plan, research decision, data entity, or contract. The parent epic stores the complete feature artifact map.

## Install

Requirements:

- Spec Kit 1.0 or newer
- Beads (`bd`) 1.0 or newer
- Python 3.10 or newer

From an initialized Spec Kit project:

```bash
specify extension add --dev /path/to/speckit-beads
```

To install from GitHub without keeping a development checkout:

```bash
specify extension add beads \
  --from https://github.com/zach-source/speckit-beads/archive/refs/heads/main.zip
```

Optionally install the Beads-native end-to-end workflow:

```bash
specify workflow add --dev /path/to/speckit-beads/workflows/beads-sdd.yml
```

## Helper CLI

Preview a generated reconciliation input:

```bash
python3 scripts/beads_tasks.py reconcile \
  --project-root /path/to/project \
  --graph /tmp/task-graph.json
```

Apply it:

```bash
python3 scripts/beads_tasks.py reconcile \
  --project-root /path/to/project \
  --graph /tmp/task-graph.json \
  --apply
```

Preview the optional Markdown projection:

```bash
python3 scripts/beads_tasks.py render \
  --project-root /path/to/project \
  --feature 001-example
```

Write the projection only when a compatibility consumer needs it:

```bash
python3 scripts/beads_tasks.py render \
  --project-root /path/to/project \
  --feature 001-example \
  --output specs/001-example/tasks.md \
  --apply
```

## Test

```bash
python3 -m unittest discover -s tests -v
```
