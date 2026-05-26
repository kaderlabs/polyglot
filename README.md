# polyglot

Makes your Claude Code skills activate — in any language.

## Background

Claude Code skill activation is keyword-based. The model reads each skill's `description` field at startup and decides whether to invoke it. Two problems consistently suppress activation:

**Language mismatch.** If your skills were authored in English and you work in another language, they won't activate — regardless of how relevant they are. Across 3,271 sessions, skills activated in 6% of them. The triggers weren't covering the language in use.

**Missing activation guidance.** The model needs a clear signal for *when* a skill applies. Without it, even well-written skills get ignored.

## How it works

polyglot runs two passes over your skill library:

1. **Language pass** — adds native-language trigger phrases to skills that lack coverage in your language. Trigger density is preserved: if a skill has 4 English triggers, it gets 4 in the target language. Patches are written to the `when_to_use` frontmatter field (the dedicated CC activation field). Idempotent. Path-based skills are skipped.

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

Claude Code's skill system has three conventions for activation guidance. They're not equivalent:

| Pattern | Example | Notes |
|---------|---------|-------|
| `when_to_use` frontmatter field | `when_to_use: "When the user asks to..."` | **Canonical.** CC's dedicated activation field — keeps `description` short for autocomplete |
| `"Use when: ..."` in `description` | `description: "Use when: \"debug\", \"trace\""` | Works but conflates listing text with activation triggers |
| `"Use this skill when ..."` in `description` | `description: "Use this skill when the user asks to build..."` | Works but same conflation issue |

The `when_to_use` field is the correct home for activation triggers. `description` is the one-line listing shown in autocomplete — keep it short. polyglot writes to `when_to_use` and detects coverage in either field.

One subtlety: CC sends skill descriptions using a delta system. Descriptions seen in a recent session are suppressed (bare skill name only); changed descriptions are sent in full. If your `description` is long and gets truncated differently across sessions, the skill perpetually looks "new" and never settles. A short `description` + separate `when_to_use` avoids this entirely.

The problem polyglot solves is that skills with *no* activation guidance — not even a hint of when they apply — compete poorly against skills that are explicit. polyglot detects and flags these.

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
