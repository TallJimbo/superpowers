# Zed Tool Mapping

Skills speak in actions ("dispatch a subagent", "create a todo", "read a file"). On the Zed Agent these resolve to the tools below.

| Action skills request                                        | Zed equivalent     |
| ------------------------------------------------------------ | ------------------ |
| Read a file                                                  | `read`             |
| Create a file                                                | `write_file`       |
| Edit a file                                                  | `edit_file`        |
| Delete a file                                                | `delete_path`      |
| Copy a file                                                  | `copy_path`        |
| Move/rename a file                                           | `move_path`        |
| Create a directory                                           | `create_directory` |
| Run a shell command                                          | `bash`             |
| Ask the user a question (primary agent only)                 | `ask_user`         |
| Search file contents                                         | `grep`             |
| Find files by name                                           | `find_path`        |
| List a directory                                             | `list_directory`   |
| Fetch a URL                                                  | `fetch`            |
| Web search                                                   | `search_web`       |
| Invoke a skill                                               | the `skill` tool   |
| Dispatch a subagent (`Subagent (general-purpose):` template) | `spawn_agent`      |

## Skill selection

Zed loads global skills from `~/.agents/skills/` (each skill is a folder there; a skill is a direct child, symlinks are supported) and project-local skills from `<worktree>/.agents/skills/`. Skills load on demand through the `skill` tool and auto-trigger when their description matches the task, so a phase agent need only select and load the correct superpowers skill for its stage.

## Notes

- Zed has no separate `apply_patch` tool; use `edit_file` and `write_file`.
- `spawn_agent` subagents get the same tools as the parent agent; there is no read-only subagent variant. To keep a reviewer read-only, instruct the subagent not to use edit tools.
- `ask_user` is intended for the **primary agent only**. Subagents also have the tool (they inherit the parent's profile, so it cannot be blocked), but it interrupts their flow and renders poorly — the skills (`zed-explorer` when used as a subagent, `zed-implementer`, `zed-reviewer`) instruct subagents to surface uncertainty in their report instead of calling it.
- Task tracking ("create a todo", "mark complete") has no dedicated Zed tool; keep a Markdown checklist in the conversation or in a file instead.
- Check compile/type errors after edits with the `diagnostics` tool.
- `search_web` is only available to Zed Pro subscribers using the zed.dev provider; otherwise use an MCP server that provides web search.

## Agent profiles

The `sp-*` agent profiles are defined under `agent.profiles` in the user settings and configure which built-in tools a thread may use. The skill to use for a phase is selected by each profile's instructions/thread context, mirroring the OpenCode `sp-*` shells.
