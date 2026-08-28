# Superpowers (personal fork) for OpenCode

Guide for using this fork of Superpowers with [OpenCode.ai](https://opencode.ai).
See the [repo README](../README.md) for what differs from upstream and the
harness-support caveat. This fork does **not** ship an OpenCode plugin; it's
installed by pointing OpenCode at the fork's skills and symlinking its agents.

## Installation

1. Clone this repository (or add it as a git remote) to
   `~/.config/opencode/superpowers`.
2. Point OpenCode at the fork's skills. In your `opencode.json` (global or
   project-level):

   ```jsonc
   {
     "skills": {
       "paths": ["~/.config/opencode/superpowers/skills"],
     },
   }
   ```

3. Symlink the `sp-*` agents into your OpenCode config:

   ```bash
   ln -sf ~/.config/opencode/superpowers/.opencode/agents/sp-*.md ~/.config/opencode/agents/
   ```

Restart OpenCode, and start with the `sp-design` agent when brainstorming a new
change.

OpenCode uses its own setup. If you also use Claude Code, Codex, or another
harness, install Superpowers separately for each one — though support for those
harnesses is unchanged from upstream and may or may not work.

## Usage

### Finding Skills

Use OpenCode's native `skill` tool to list all available skills:

```
use skill tool to list skills
```

### Loading a Skill

```
use skill tool to load brainstorming
```

### Personal Skills

Create your own skills in `~/.config/opencode/skills/`:

```bash
mkdir -p ~/.config/opencode/skills/my-skill
```

Create `~/.config/opencode/skills/my-skill/SKILL.md`:

```markdown
---
name: my-skill
description: Use when [condition] - [what it does]
---

# My Skill

[Your skill content here]
```

### Project Skills

Create project-specific skills in `.opencode/skills/` within your project.

**Skill Priority:** Project skills > Personal skills > Superpowers skills

## Updating

This fork has no plugin to update. To pick up new commits, `git pull` in
`~/.config/opencode/superpowers` and restart OpenCode.

## How It Works

The fork relies on two OpenCode config features rather than a plugin:

1. **`skills.paths`** registers the fork's `skills/` directory, so OpenCode
   discovers all skills without symlinks.
2. **Symlinked `sp-*` agents** in `~/.config/opencode/agents/` provide the custom
   workflow agents.

### Tool Mapping

Skills speak in actions rather than naming any one runtime's tools. On OpenCode these resolve to:

- "Create a todo" / "mark complete in todo list" → `todowrite`
- `Subagent (general-purpose):` template → OpenCode's `task` tool with `subagent_type: "general"` (or `"explore"` for codebase exploration)
- "Invoke a skill" → OpenCode's native `skill` tool
- "Read a file" → `read`
- "Create a file" / "edit a file" / "delete a file" → `apply_patch`
- "Run a shell command" → `bash`
- "Search file contents" / "find files by name" → `grep`, `glob`
- "Fetch a URL" → `webfetch`

(Verified against the installed OpenCode CLI's tool inventory.)

## Troubleshooting

### Skills not found

1. Use OpenCode's `skill` tool to list available skills
2. Check that `skills.paths` in your `opencode.json` points at the fork's `skills/`
3. Each skill needs a `SKILL.md` file with valid YAML frontmatter

### Agents not found

1. Verify the `sp-*.md` symlinks exist in `~/.config/opencode/agents/`
2. Restart OpenCode after config changes

## Getting Help

- Upstream Superpowers: https://github.com/obra/superpowers
- OpenCode docs: https://opencode.ai/docs/
