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

## Notes

- OpenCode has no separate `apply_patch` tool; use `write` and `edit`.
- Skills register via `config.skills.paths` in `opencode.json` (no plugin needed).
