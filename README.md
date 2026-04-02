# Work Style Memory

`work-style-memory` is a local skill for turning repeated multi-step tasks into reusable workflows.

It helps an agent:

- detect when a new task looks similar to a saved SOP
- ask the user whether to reuse that SOP before execution
- render a saved workflow into a short execution brief
- save or update a workflow after a task is completed

The workflow library is stored as local JSON files so it can be edited by both scripts and a lightweight local UI.

## Features

- workflow matching for repeated tasks
- explicit confirmation before reusing a matched SOP
- workflow-to-brief rendering for runtime execution
- draft capture and save / update flow after real tasks
- local UI for browsing, editing, deleting, and previewing workflows
- Unicode-friendly workflow names and file paths

## Project Structure

```text
work-style-memory/
├── SKILL.md
├── instruction.md
├── assets/
├── references/
├── scripts/
└── ui/
```

## Workflow Shape

Each workflow is stored as one JSON file.

```json
{
  "id": "pr-review",
  "name": "PR Review",
  "summary": "Review pull requests with a risk-first pass.",
  "match": {
    "keywords": ["pr", "review", "diff"]
  },
  "steps": [
    {
      "id": "step-1",
      "title": "Step 1",
      "instruction": "Fetch the PR",
      "tool": "github"
    }
  ],
  "tool_preferences": [
    {
      "tool": "github",
      "purpose": ""
    }
  ],
  "version": 1
}
```

## Quick Start

Create a workflow:

```bash
python3 scripts/new_workflow.py "PR Review" --dir /tmp/workflows
```

Match a task:

```bash
python3 scripts/match_workflows.py \
  "Please review this PR and look for regressions" \
  --dir /tmp/workflows \
  --tools git,github
```

Render an execution brief:

```bash
python3 scripts/render_workflow_prompt.py \
  /tmp/workflows/pr-review.json \
  --task "Please review this PR and look for regressions"
```

Save a workflow from a real task:

```bash
python3 scripts/save_workflow.py \
  "Review this PR for regressions" \
  --dir /tmp/workflows \
  --steps $'Fetch PR\nInspect risky changes\nWrite findings' \
  --tools git,github
```

## Local UI

Start the editor:

```bash
python3 ui/server.py --dir ./.openclaw/workflows
```

Then open `http://127.0.0.1:8765`.

The UI is intended for workflow management only. Runtime matching and reuse prompts belong in the skill's chat behavior, not in the editor itself.

## Runtime Rules

- Check saved workflows before a non-trivial repeated task.
- If there is a strong match, ask the user whether to reuse it.
- Do not silently execute a matched SOP unless the user explicitly asked to use their previous workflow in the current turn.
- After a meaningful multi-step task, ask whether the process should be saved or used to update an existing workflow.

## Installation

Copy the folder into your host product's local skills directory and restart the host or open a new session.

```bash
cp -R work-style-memory /path/to/skills/
```

## Documentation

- [instruction.md](./instruction.md)
- [references/workflow-schema.md](./references/workflow-schema.md)
- [references/runtime-behavior.md](./references/runtime-behavior.md)
- [references/local-ui-spec.md](./references/local-ui-spec.md)

## License

[MIT](./LICENSE)
