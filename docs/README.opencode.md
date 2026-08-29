# Superpowers (personal fork) for OpenCode

Guide for using this fork of Superpowers with [OpenCode.ai](https://opencode.ai).
See the [repo README](../README.md) for what differs from upstream and the
harness-support caveat. This fork does **not** ship an OpenCode plugin; it's
installed by pointing OpenCode at the fork's skills.

## Installation

Point OpenCode at the fork's skills. In your `opencode.json` (global or
project-level):

```jsonc
{
  "skills": {
    "paths": ["<tkt>/superpowers/skills"],
  },
}
```

Restart OpenCode.

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

This fork has no plugin to update. To pick up new commits, update the submodule
in the `tkt` package (`git submodule update --remote superpowers`) and restart
OpenCode.

## How It Works

The fork relies on OpenCode config rather than a plugin: `skills.paths` registers
the fork's `skills/` directory, so OpenCode discovers all skills without
symlinks.

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

## Getting Help

- Upstream Superpowers: https://github.com/obra/superpowers
- OpenCode docs: https://opencode.ai/docs/
