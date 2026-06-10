---
name: fableplan
description: Toggle "Fable Plan Mode" — plan with Fable 5, execute with Sonnet (opusplan-style). Use when the user runs /fableplan, optionally with "off" to revert, or "1m" to use the 1M-context Fable variant.
---

# Fableplan — plan with Fable 5, execute with Sonnet

Claude Code has a built-in `opusplan` model setting ("Opus in plan mode, Sonnet otherwise") but no Fable equivalent. This skill recreates it by combining `opusplan` with the `ANTHROPIC_DEFAULT_OPUS_MODEL` environment variable override, which redirects the "opus" alias to Fable 5.

The target file is the user's **global** settings: `~/.claude/settings.json`.

## Arguments

- *(none)* — enable fableplan with `claude-fable-5`
- `1m` — enable fableplan with `claude-fable-5[1m]` (1M context window)
- `off` — revert to the previous configuration

## Enabling (no argument, or `1m`)

1. Read `~/.claude/settings.json`. If it does not exist, create it with an empty object first.
2. Merge the following keys, preserving every other existing setting:
   - If a `model` key already exists and is different from `"opusplan"`, save its current value under the key `fableplanPreviousModel` (top-level, custom key — extra keys are allowed in settings.json) so it can be restored later.
   - Set `"model": "opusplan"`.
   - Under `"env"` (create the object if missing, preserve its other entries), set `"ANTHROPIC_DEFAULT_OPUS_MODEL"` to `"claude-fable-5"` (or `"claude-fable-5[1m]"` if the argument is `1m`).
3. Validate that the resulting file is valid JSON.
4. Tell the user:
   - Fableplan is enabled: plan mode runs on Fable 5, execution runs on Sonnet.
   - They must **restart their Claude Code session** for the model change to take effect.
   - The UI will display "Opus Plan Mode" — that label is cosmetic; plan mode actually runs Fable 5.

## Disabling (`off`)

1. Read `~/.claude/settings.json`.
2. Remove `"ANTHROPIC_DEFAULT_OPUS_MODEL"` from `"env"`. If `"env"` becomes empty, remove the `"env"` key entirely.
3. Restore the model:
   - If `fableplanPreviousModel` exists, set `"model"` to that value and remove the `fableplanPreviousModel` key.
   - Otherwise remove the `"model"` key (falls back to the account default) and tell the user they can pick a model with `/model`.
4. Validate JSON and tell the user to restart the session.

## Important caveats (mention them when enabling for the first time)

- `ANTHROPIC_DEFAULT_OPUS_MODEL` is an undocumented override — a future CLI update could change or remove it.
- Fable 5 is not available on every plan/account. If requests fail after enabling, run `/fableplan off` to revert.
- Anything else that resolves the "opus" alias (e.g. fast mode) will also point to Fable while enabled.
