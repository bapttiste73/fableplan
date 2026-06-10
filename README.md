# fableplan

**Plan with Fable 5, execute with Sonnet** — an [opusplan](https://docs.claude.com/en/docs/claude-code)-style dual-model setup for Claude Code.

Claude Code ships with a built-in `opusplan` model setting: *"Opus in plan mode, Sonnet otherwise"*. There is no Fable equivalent in the model picker — the model list is hardcoded in the CLI. This plugin recreates it with a small trick: `opusplan` resolves its plan-mode model through the `ANTHROPIC_DEFAULT_OPUS_MODEL` environment variable, so pointing that variable at Fable 5 gives you **Fable 5 in plan mode, Sonnet for execution**.

## Install

```
/plugin marketplace add bapttiste73/fableplan
/plugin install fableplan@fableplan
```

## Usage

| Command | Effect |
|---|---|
| `/fableplan` | Enable: plan with `claude-fable-5`, execute with Sonnet |
| `/fableplan 1m` | Same, with the 1M-context Fable variant (`claude-fable-5[1m]`) |
| `/fableplan off` | Revert to your previous model configuration |

Restart your Claude Code session after toggling — the model setting is read at startup.

## How it works

The skill merges two keys into your global `~/.claude/settings.json` (everything else is preserved):

```json
{
  "model": "opusplan",
  "env": {
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "claude-fable-5"
  }
}
```

That's it. You can also apply this by hand without installing anything.

## Caveats

- The UI will display **"Opus Plan Mode"** — the label is cosmetic; plan mode actually runs Fable 5.
- `ANTHROPIC_DEFAULT_OPUS_MODEL` is an undocumented override and could change in a future CLI release.
- Fable 5 is not available on every plan/account. If requests fail, run `/fableplan off`.
- While enabled, anything else that resolves the "opus" alias (e.g. fast mode) also points to Fable.

## License

MIT
