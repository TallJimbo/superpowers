# OpenCode Tool Mapping

Skills speak in actions ("dispatch a subagent", "create a todo", "read a file"). On OpenCode these resolve to the tools below.

| Action skills request | OpenCode equivalent |
|----------------------|---------------------|
| Read a file | `read` |
| Create a file | `write` |
| Edit a file | `edit` |
| Delete a file | `bash` (e.g. `rm`) or `write` for overwrite |
| Run a shell command | `bash` |
| Search file contents | `grep` |
| Find files by name | `glob` |
| Fetch a URL | `webfetch` |
| Ask structured questions | `question` |
| Invoke a skill | the `skill` tool |
| Dispatch a subagent (`Subagent (general-purpose):` template) | `task` with `subagent_type` — `general` for full-capability work, `explore` for read-only codebase exploration, `sp-review` for independent review |
| Task tracking ("create a todo", "mark complete") | `todowrite` (statuses: pending, in_progress, completed, cancelled) |

## Skill selection

OpenCode loads skills through the `skill` tool. The intent-signaling `sp-*`
agents select and load the relevant skill for their phase:

- `sp-design` → loads `brainstorming` (conversational design; no durable writes)
- `sp-plan` → loads `writing-plans` (materializes design doc + plan)
- `sp-build` → loads `subagent-driven-development`
- `sp-review` → read-only reviewer dispatched by `sp-build`

These shells live in `~/.config/opencode/agents/`. There is no bootstrap
injection; each shell's prompt carries the tool mapping and skill selection.

## Instructions file

When a skill mentions "your instructions file", on OpenCode this is the project's
`AGENTS.md` (loaded hierarchically). Project-specific concerns such as branch or
worktree isolation are specified in `AGENTS.md`.

## Model policy

All subagents run on the machine-designated local model — **never** an
OpenCode Zen or Princeton AI Sandbox model, at the primary agent, for any
subagent, in any situation.

- Designated model: `rubin-dm-01/local-inference-lab/Qwen3.8-Flash-Next-NVFP4`
  (Broadmead DGX Spark). The OpenCode catalog is allowlisted to this
  provider: naming any other provider's model in a `task` dispatch is a hard
  error by design.
- Default dispatch: **omit** the `model` parameter. The `agent` config and
  agent frontmatter pin the designated model for `general`, `explore`, and
  `sp-review` at the `medium` effort variant, and an omitted parameter runs
  the subagent on that pin.
- Vary reasoning effort by passing the designated id with a variant suffix:
  `rubin-dm-01/local-inference-lab/Qwen3.8-Flash-Next-NVFP4#low`,
  `#medium`, or `#xhigh` — the full designated id plus the suffix, never the
  suffix alone. This is the only permitted reason to set the parameter.
- Never use the `models` tool to shop for models. If a dispatch errors that a
  model is unavailable, you named an undesignated model: re-read this policy
  and re-dispatch — do not retry with another id.

## Notes

- OpenCode has no separate `apply_patch` tool; use `write` and `edit`.
- Skills register via `config.skills.paths` in `opencode.json` (no plugin needed).
