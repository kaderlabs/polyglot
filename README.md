# polyglot

Adds multilingual trigger phrases to Claude Code skills.

## Background

Claude Code skill activation is keyword-based. The model reads each skill's `description` field and decides whether to invoke it. If your skills were authored in English and you work in another language, they won't activate — regardless of how relevant they are.

Across 3,271 sessions, skills activated in 6% of them. The skills were correct. The triggers weren't covering the language in use.

## How it works

polyglot scans your skill library, reads each `description` field, detects existing language coverage by character set, and generates native-language trigger phrases for skills that lack it. Trigger density is preserved — if a skill has 4 English triggers, it gets 4 in the target language.

Patches are written directly to the `description` frontmatter field. Idempotent. Path-based skills (triggered by file patterns, not keywords) are skipped automatically.

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

Language codes follow [ISO 639-1](LANGUAGES.md) — the same two-letter standard used everywhere. See [LANGUAGES.md](LANGUAGES.md) for all 184 codes.

## Language detection

Built-in character-set detection for `sv` `fr` `de` `es`. Any other language is supported — polyglot uses Claude to generate the triggers and marks coverage with an `Also ([lang]):` prefix in the description.

## Output

```
polyglot — sv · 2026-05-26
──────────────────────────────────────
✓  code-review    +4 triggers
✓  research       +6 triggers
──────────────────────────────────────
2 updated
```

## License

MIT
