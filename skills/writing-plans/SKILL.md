---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** Isolation (branch/worktree) is project-specific and specified by the
project's AGENTS.md instructions, already in your context. Do not create a
worktree or ask about one.

## Materialize the design handover doc first

You run in the same session as the design agent, so the approved design is in
your conversation. Before writing the plan, materialize it as a **design handover
doc**:

- Save to `$SUPERPOWERS_DIR/specs/YYYY-MM-DD-<feature-name>-design.md` if the
  `SUPERPOWERS_DIR` environment variable is set (it points at the shared docs
  repo's per-ticket namespace), otherwise
  `docs/superpowers/specs/YYYY-MM-DD-<feature-name>-design.md` (in-repo default).
- Capture the agreed design, the decisions made, and any code examples /
  prototype stubs **verbatim** from the conversation. Those snippets are
  authoritative: the implementing agent will transcribe them, not reinvent them.
- Self-check it for placeholders, contradictions, and ambiguity before moving on.

**This document is for the implementing agents, not for the human.** Its job is
carrying the design across compaction/new-session handover and into
implementation. The human does not read or maintain it; they already approved the
design in conversation.

**Save plans to:** `$SUPERPOWERS_DIR/plans/YYYY-MM-DD-<feature-name>.md` if the
`SUPERPOWERS_DIR` environment variable is set (it points at the shared docs
repo's per-ticket namespace), otherwise
`docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md` (in-repo default).

## The plan is a hidden agent artifact

The plan document is internal — the human does not read or review it. Code is the
source of truth and the thing they review. The human's review surface is the
conversation: **surface each design-level decision as you make it in chat and get
immediate sign-off** — choices that affect behavior, interfaces, structure, or
tradeoffs (file layout, signatures, reinterpretations), not mechanical
transcription. Because decisions are signed off as they're made, the final gate
before implementation (Gate 2) is usually just a confirmation. When the plan's
task text contains the complete code to write (from the design handover or
derived during planning), the implementing agent transcribes it verbatim.

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Task Right-Sizing

A task is the smallest unit that carries its own test cycle and is worth a
fresh reviewer's gate. When drawing task boundaries: fold setup,
configuration, scaffolding, and documentation steps into the task whose
deliverable needs them; split only where a reviewer could meaningfully
reject one task while approving its neighbor. Each task ends with an
independently testable deliverable.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**Spec:** [path to the spec/design doc this plan implements — the plan
argues from the spec, so the spec travels with it; executors read both]

## Global Constraints

[The spec's project-wide requirements — version floors, dependency limits,
naming and copy rules, platform requirements — one line each, with exact
values copied verbatim from the spec. Every task's requirements implicitly
include this section.]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Interfaces:**
- Consumes: [what this task uses from earlier tasks — exact signatures]
- Produces: [what later tasks rely on — exact function names, parameter
  and return types. A task's implementer sees only their own task; this
  block is how they learn the names and types neighboring tasks use.]

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — the engineer may be reading tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Self-Review

After writing the complete plan, look at the spec with fresh eyes and check the plan against it. This is a checklist you run yourself — not a subagent dispatch.

**1. Spec coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**2. Placeholder scan:** Search your plan for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**3. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

If you find issues, fix them inline. No need to re-review — just fix and move on. If you find a spec requirement with no task, add the task.

## Execution Handoff

After saving the plan, present the Gate 2 summary in chat: a concise recap of the
design decisions you made (beyond the brainstormed design) and the task structure,
and get the human's go-ahead before implementation. This is also the natural
handoff/compaction point — the design doc and plan together carry a fresh session.

**"Design doc and plan complete and saved to `$SUPERPOWERS_DIR/specs/` and
`$SUPERPOWERS_DIR/plans/` (or `docs/superpowers/` if `SUPERPOWERS_DIR` is unset).
Here are the decisions and task breakdown — any concerns before I start building?
Switch to the sp-build agent to execute it once approved."**

- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Fresh subagent per task + two-stage review
