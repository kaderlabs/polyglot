# polyglot

A Claude Code skill that adds multilingual triggers to your other skills.

## The problem

I had 42 skills. Carefully built, regularly pruned. Thought I was getting value out of them.

Then I queried my session history: 192 out of 3,271 sessions had used a skill. That's 6%.

The skills weren't broken. The triggers were in English. I think and write in Swedish.

Claude Code skill activation is keyword-based — the model reads the `description` field and decides if a skill applies. If your description says `"refactor"` and you write `"refaktorera"`, nothing fires.

The fix isn't rewriting your skills. It's teaching them to recognize you.

## What it does

Scans your skill library, detects which skills lack coverage for your target language, generates contextual native-language triggers for each one, and patches the `description` field in-place.

Matches trigger density — if a skill has 4 English triggers, it adds 4 in your language. No bloat.

Idempotent. Skips path-based skills. Only reports what changed.

## Install

```bash
git clone https://github.com/kaderlabs/polyglot ~/.claude/skills/polyglot
```

## Usage

```
/polyglot sv                       # Swedish
/polyglot fr                       # French
/polyglot de                       # German
/polyglot sv --dry-run             # Preview without writing
/polyglot sv --skill my-skill      # Single skill only
```

Language is required — no default.

## Supported languages

Any language Claude can write in. Built-in coverage detection for `sv` `fr` `de` `es` — others detected via `Also ([lang]):` markers in the description.

## Example output

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
