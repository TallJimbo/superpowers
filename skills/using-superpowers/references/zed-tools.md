# Zed Tool Mapping

Skills speak in actions ("dispatch a subagent", "create a todo", "read a file"). On the Zed Agent these resolve to the tools below.

| Action skills request                                        | Zed equivalent     |
| ------------------------------------------------------------ | ------------------ |
| Read a file                                                  | `read`             |
| Create a file                                                | `write`            |
| Edit a file                                                  | `edit`             |
| Delete a file                                                | `delete_path`      |
| Copy a file                                                  | `copy_path`        |
| Move/rename a file                                           | `move_path`        |
| Create a directory                                           | `create_directory` |
| Run a shell command                                          | `bash`             |
| Ask the user a question (primary agent only)                 | `ask_user`         |
| Search file contents                                         | `grep`             |
| Find files by name                                           | `glob`             |
| List a directory                                             | `ls`               |
| Create/update the task todo list                             | `todo_write`       |
| Fetch a URL                                                  | `fetch`            |
| Web search                                                   | `search_web`       |
| Invoke a skill                                               | the `skill` tool   |
| Dispatch a subagent (`Subagent (general-purpose):` template) | `spawn_agent`      |

## Skill selection

Zed loads global skills from `~/.agents/skills/` (each skill is a folder there; a skill is a direct child, symlinks are supported) and project-local skills from `<worktree>/.agents/skills/`. Skills load on demand through the `skill` tool and auto-trigger when their description matches the task, so a phase agent need only select and load the correct superpowers skill for its stage.

## Model policy

All subagents run on the machine-designated local model (Broadmead DGX
Spark), pinned machine-side by `agent.subagent_model` in Zed settings —
**never** a cloud-provider model, chosen by the primary agent or anyone else.

- Always **omit** the `model` parameter when calling `spawn_agent`. The
  machine pin applies automatically and resolves to the designated local
  model; the primary's own model and effort settings do not need to reach the
  subagent to satisfy this policy.
- Never name a model id, never browse models, and never invoke
  `list_agents_and_models` — it is disabled here on purpose. If an error
  message mentions listing models, you passed a `model` you should not have:
  drop the parameter and re-spawn — do not ask to enable a listing tool.
- Zed has no per-spawn reasoning-effort parameter: subagent effort comes from
  the machine pin. Do not attempt to set or vary it per dispatch. Where a
  skill talks about scaling effort tiers per role, on Zed that choice is
  already made machine-side.

## Notes

- Zed has no separate `apply_patch` tool; use the sandboxed MCP `write` and `edit` tools (which run inside the tkt sandbox).
- `spawn_agent` subagents get the same tools as the parent agent; there is no read-only subagent variant. To keep a reviewer read-only, instruct the subagent not to use edit tools.
- `ask_user` is intended for the **primary agent only**. Subagents also have the tool (they inherit the parent's profile, so it cannot be blocked), but it interrupts their flow and renders poorly — the skills (`zed-explorer` when used as a subagent, `zed-implementer`) instruct subagents to surface uncertainty in their report instead of calling it. (`zed-reviewer` is a primary-only skill, so it may use `ask_user` freely.)
- Task tracking ("create a todo", "mark complete") maps to the `todo_write`
  tool — a whole-list-replace scratchpad held in the tkt MCP server's memory
  for the session.
- Check compile/type errors after edits with the `diagnostics` tool.
- `search_web` is only available to Zed Pro subscribers using the zed.dev provider; otherwise use an MCP server that provides web search.
