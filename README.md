# kiro-antigravity-bridge

A file-based communication bridge that enables two-way task delegation between [Kiro](https://kiro.dev) and [Antigravity](https://antigravity.google) IDE.

## How It Works

Both IDEs share the same project folder. They communicate by reading and writing Markdown files in the `.agents/` directory.

```
.agents/
  ├── antigravity/
  │   ├── AGENTS.md    # Instructions for Antigravity agent
  │   ├── task.md      # Kiro writes tasks → Antigravity reads
  │   ├── result.md    # Antigravity writes responses → Kiro reads
  │   └── status.md    # Antigravity's current status
  ├── kiro/
  │   ├── task.md      # Antigravity writes tasks → Kiro reads
  │   ├── result.md    # Kiro writes responses → Antigravity reads
  │   └── status.md    # Kiro's current status
  ├── context/
  │   ├── project.md        # Tech stack, architecture overview
  │   ├── conventions.md    # Coding style, naming rules
  │   ├── current-sprint.md # Active goals, in-progress features
  │   └── decisions.md      # Key decisions already made
  ├── history/         # Archived task/result pairs (gitignored)
  ├── scripts/
  │   ├── archive.sh / archive.cmd   # Archive completed exchanges
  │   ├── reset.sh / reset.cmd       # Reset statuses and clear files
  │   └── pre_invocation.js          # Antigravity pre-invocation hook
  └── hooks.json       # Antigravity hook configuration
```

## Workflows

### Kiro → Antigravity

1. Kiro writes a task to `.agents/antigravity/task.md`
2. Kiro runs Antigravity CLI as a background process (`agy --print`)
3. Antigravity processes the task and writes the result to `.agents/antigravity/result.md`
4. Kiro reads the result when the user requests it

### Antigravity → Kiro

1. Antigravity writes a task to `.agents/kiro/task.md`
2. Kiro hook (`fileEdited`) auto-triggers and processes the task
3. Kiro writes the result to `.agents/kiro/result.md`
4. Antigravity reads the result

## Setup

### Prerequisites

- [Kiro IDE](https://kiro.dev)
- [Antigravity IDE](https://antigravity.google) or [Antigravity CLI](https://antigravity.google) (`agy`)
- Node.js (for Antigravity pre-invocation hook)

### Installation

1. Copy the `.agents/`, `.kiro/` folders into your project root
2. Open the project in both Kiro and Antigravity IDE
3. Antigravity will auto-load `.agents/hooks.json` and inject pending tasks on startup via `pre_invocation.js`

### Kiro Steering (optional)

Load the steering file in Kiro chat with `#agent-collaboration` to give Kiro full context on the workflow. The steering file is located at `.kiro/steering/agent-collaboration.md`.

## Scripts

| Script | Description |
|--------|-------------|
| `archive.sh` / `archive.cmd` | Archive a completed task/result pair to `history/` |
| `reset.sh` / `reset.cmd` | Reset agent statuses and clear task/result files |

```bash
# Archive (Linux/macOS)
./.agents/scripts/archive.sh antigravity 2026-05-24T13-00-00

# Archive (Windows)
.agents\scripts\archive.cmd antigravity 2026-05-24T13-00-00

# Reset all (Linux/macOS)
./.agents/scripts/reset.sh

# Reset all (Windows)
.agents\scripts\reset.cmd
```

## Task Format

```markdown
## Task: <title>
**File:** <relative path to relevant file, if any>
**Context:** <brief context the other agent needs>
**Question/Action:** <exactly what the other agent should do>
**Task-ID:** <timestamp, e.g. 2026-05-24T13-00-00>
```

## Result Format

```markdown
## Result: <task title>
**Status:** Completed / Partial / Failed
**Task-ID:** <same id from task>
**Response:**
<full response here>
```

## Files

| File | Description |
|------|-------------|
| `.agents/antigravity/task.md` | Task queue from Kiro to Antigravity |
| `.agents/antigravity/result.md` | Response from Antigravity to Kiro |
| `.agents/antigravity/status.md` | Antigravity's current status (`idle` / `processing` / `done` / `error`) |
| `.agents/antigravity/AGENTS.md` | Instructions for Antigravity agent (auto-loaded as system context) |
| `.agents/kiro/task.md` | Task queue from Antigravity to Kiro |
| `.agents/kiro/result.md` | Response from Kiro to Antigravity |
| `.agents/kiro/status.md` | Kiro's current status (`idle` / `processing` / `done` / `error`) |
| `.agents/context/project.md` | Project tech stack and architecture |
| `.agents/context/conventions.md` | Coding style and naming rules |
| `.agents/context/current-sprint.md` | Active goals and in-progress features |
| `.agents/context/decisions.md` | Key architectural decisions |
| `.agents/history/` | Archived task/result pairs (gitignored) |
| `.agents/scripts/archive.sh` / `.cmd` | Archive a completed task/result pair to `history/` |
| `.agents/scripts/reset.sh` / `.cmd` | Reset agent statuses and clear task/result files |
| `.agents/scripts/pre_invocation.js` | Antigravity pre-invocation hook — injects pending tasks on startup |
| `.agents/hooks.json` | Antigravity hook configuration |
| `.kiro/hooks/kiro-process-antigravity-task.kiro.hook` | Kiro hook — auto-processes tasks from Antigravity |
| `.kiro/steering/agent-collaboration.md` | Kiro steering — workflow guidelines for Kiro agent |
