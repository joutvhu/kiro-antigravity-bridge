# Agent Collaboration

This folder enables two-way communication between Kiro and Antigravity CLI.

## Structure

```
.agents/
  ├── kiro/
  │   ├── task.md      # Antigravity writes tasks for Kiro
  │   └── result.md    # Kiro writes responses for Antigravity
  └── antigravity/
      ├── task.md      # Kiro writes tasks for Antigravity
      └── result.md    # Antigravity writes responses for Kiro
```

## Workflow: Kiro → Antigravity

1. Kiro writes a task to `.agents/antigravity/task.md`
2. Hook auto-triggers `agy --print` to process the task
3. Antigravity reads the task, works on it, writes output to `.agents/antigravity/result.md`
4. Kiro reads `.agents/antigravity/result.md` and continues

## Workflow: Antigravity → Kiro

1. Antigravity writes a task to `.agents/kiro/task.md`
2. Hook auto-triggers Kiro agent to process the task
3. Kiro reads the task, works on it, writes output to `.agents/kiro/result.md`
4. Antigravity reads `.agents/kiro/result.md` and continues

## Task Format

```markdown
## Task: <title>
**File:** <relative path to relevant file>
**Context:** <brief context to share>
**Question/Action:** <what the other agent should do>
```


