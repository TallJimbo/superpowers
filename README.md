# Superpowers (personal fork)

A personal fork of [Superpowers](https://github.com/obra/superpowers) that may
be useful as a starting point for others. It keeps most of upstream's skills
and philosophy but retargets the workflow around a small set of custom agents.

## What's different from upstream

- **The design phase, and code-as-central.** Upstream treats the written spec as
  the source of truth. This fork reworks the design phase so the _conversation_
  is where the human review happens and code is the source of truth.
- **Custom `sp-*` agents.** Adds `sp-design`, `sp-plan`, `sp-build`, `sp-debug`,
  and `sp-review` (in `.opencode/agents/`) that implement this workflow for
  OpenCode, and prunes the skills they don't use.
- **Harness support.** OpenCode (with the `tkt` sandbox) is what's supported and
  used now. Support for other harnesses is unchanged from upstream and may or
  may not work.

## Installation (OpenCode)

1. Clone this repository (or add it as a git remote) to
   `~/.config/opencode/superpowers`.
2. Point OpenCode at the fork's skills. In `opencode.json`:

   ```jsonc
   {
     "skills": {
       "paths": ["~/.config/opencode/superpowers/skills"],
     },
   }
   ```

3. Symlink the agents into your OpenCode config:

   ```bash
   ln -sf ~/.config/opencode/superpowers/.opencode/agents/sp-*.md ~/.config/opencode/agents/
   ```

Restart OpenCode.

## What's inside

The retained skills are a trimmed subset of upstream, pruned to what the `sp-*`
agents use:

- **Design & planning:** `brainstorming`, `writing-plans`
- **Development:** `subagent-driven-development`, `test-driven-development`
- **Debugging:** `systematic-debugging`, `verification-before-completion`
- **Review & completion:** `requesting-code-review`, `finishing-a-development-branch`
- **Meta:** `using-superpowers`, `writing-skills`

See the upstream [README](https://github.com/obra/superpowers) for the full
Superpowers methodology and the skills this fork removed.

## License

MIT — see [LICENSE](LICENSE).
