# polyglot

Makes your Claude Code skills activate — in any language.

## Background

Claude Code skill activation is keyword-based. The model reads each skill's `description` field at startup and decides whether to invoke it. Two problems consistently suppress activation:

**Language mismatch.** If your skills were authored in English and you work in another language, they won't activate — regardless of how relevant they are. Across 3,271 sessions, skills activated in 6% of them. The triggers weren't covering the language in use.

**Missing activation guidance.** The model needs a clear signal for *when* a skill applies. Without it, even well-written skills get ignored.

## How it works

polyglot runs two passes over your skill library:

1. **Language pass** — adds native-language trigger phrases to skills that lack coverage in your language. Trigger density is preserved: if a skill has 4 English triggers, it gets 4 in the target language. Patches are written directly to the `description` frontmatter field. Idempotent. Path-based skills are skipped.

2. **Activation check** — flags skills with no activation guidance at all, then asks if you want to add it.

## Install

```bash
git clone https://github.com/kaderlabs/polyglot ~/.claude/skills/polyglot
```

## Usage

```
/polyglot sv                            # Swedish
/polyglot fr                            # French
/polyglot de                            # German
/polyglot sv --dry-run                  # Preview without writing
/polyglot sv --skill my-skill           # Single skill only
```

Language codes follow [ISO 639-1](LANGUAGES.md). See [LANGUAGES.md](LANGUAGES.md) for all 184 codes.

## Language detection

Built-in character-set detection for `sv` `fr` `de` `es`. Any other language is supported — polyglot uses Claude to generate the triggers and marks coverage with an `Also ([lang]):` prefix in the description.

## How skill activation actually works

Claude Code's skill system has three conventions for activation guidance — and most skill libraries mix all three:

| Pattern | Example | Notes |
|---------|---------|-------|
| `"Use when: ..."` in `description` | `description: "Use when: \"debug\", \"trace\""` | Common in community skills |
| `"Use this skill when ..."` in `description` | `description: "Use this skill when the user asks to build..."` | Pattern used by Claude Code's own built-in skills |
| `when_to_use` frontmatter field | `when_to_use: "When the user asks to..."` | Separate field per spec, rarely used in practice |

All three work. None is wrong. The problem is that skills with *no* activation guidance — not even a hint of when they apply — compete poorly against skills that are explicit. polyglot detects and flags these.

## Output

```
polyglot — sv · 2026-05-26
──────────────────────────────────────
✓  code-review    +4 triggers
✓  research       +6 triggers
──────────────────────────────────────
2 updated

⚠  Skills with no activation guidance:
   lint, test
   Add "Use when" descriptions? (y/n)
──────────────────────────────────────
```

## License

MIT
