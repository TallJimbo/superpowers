# Superpowers (personal fork)

A fork of [Superpowers](https://github.com/obra/superpowers) that reworks the
design phase so the conversation is where the human review happens and code is
the source of truth.

The intent is to use SDD for context management and discipline only.

- **Harness support.**

## Installation

OpenCode (with the `tkt` sandbox) is what's supported and used now. Support for
other harnesses is unchanged from upstream and may or may not work.

The `skills/` directory is wired into the host environment by
`~/.config/opencode/opencode.jsonc`:

```jsonc
{
  "skills": {
    "paths": ["<tkt>/superpowers/skills"],
  },
}
```

Restart OpenCode.

## License

MIT — see [LICENSE](LICENSE).
