# polyglot

A Claude Code skill that adds multilingual triggers to your other skills.

## The problem

Claude Code skills activate based on keywords in their `description` field. If your skills were written in English and you think in Swedish (or French, German, Spanish...), they won't trigger — even when you need them.

Audit of a real skill library: **6% of sessions used skills**. The fix isn't rewriting your skills. It's teaching them to recognize you.

## What it does

Scans your skill library, detects which skills lack coverage for your target language, generates contextual native-language triggers for each one, and patches the `description` field in-place.

Idempotent. Skips path-based skills. Reports exactly what changed.

## Install

```bash
git clone https://github.com/fawzinygren/polyglot ~/.claude/skills/polyglot
```

Or into a custom skills directory:

```bash
git clone https://github.com/fawzinygren/polyglot ~/.agents/skills/polyglot
```

## Usage

```
/polyglot                          # Add Swedish triggers to all skills
/polyglot fr                       # French
/polyglot de                       # German
/polyglot sv --dry-run             # Preview without writing
/polyglot sv --skill debug-like-expert  # Single skill only
```

## Supported languages

Any language Claude can write in. Built-in coverage detection for:
`sv` `fr` `de` `es` — others detected via `Also ([lang]):` markers.

## How it works

1. Finds all `SKILL.md` files across standard skill directories
2. Reads the `description` frontmatter field
3. Detects existing language coverage by character set
4. For each skill missing coverage: generates 8–12 contextual trigger phrases
5. Appends them to the description: `Also: "phrase1", "phrase2", ...`
6. Reports a summary of what changed

## Example output

```
polyglot — sv · 2026-05-26
──────────────────────────────────────
✓  software-design      +10 triggers
✓  debug-like-expert    +9 triggers
✓  deep-research        +8 triggers
○  intygio-trust-code   skipped (path-based)
◆  linkedin             already covered
──────────────────────────────────────
3 updated · 1 skipped · 1 already covered
```

## License

MIT
