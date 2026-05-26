---
name: polyglot
description: Add multilingual trigger phrases to Claude Code skills so they activate when you write in any language. Use when skills don't trigger because you wrote in a non-English language. Triggers: "polyglot", "add triggers", "localize skills", "skills don't trigger in [language]", "lägg till svenska triggers", "skills triggar inte på svenska", "triggers saknas", "add [language] to skills", "lägger inte till svenska".
---

<objective>
Scan your Claude Code skill library and append native-language trigger phrases to any skill whose description only covers English. Skills activate naturally when you think and write in your preferred language — without rewriting the skills themselves.

Language is required. No default.
</objective>

<invocation>
- `/polyglot sv` — add Swedish triggers to all skills missing them
- `/polyglot fr` — French
- `/polyglot sv --dry-run` — preview without writing
- `/polyglot sv --skill software-design` — single skill only

If no language is given, ask: "Which language? (e.g. sv, fr, de, es)"
</invocation>

<process>

## 1 — Locate skills

```bash
find ~/.claude/skills ~/.agents/skills ~/ace/skills -name "SKILL.md" 2>/dev/null
```

If `--skill <name>` is passed, filter to that skill only.

## 2 — Detect coverage

For each SKILL.md, read the `description` frontmatter field.

Language markers:
- **sv** — `å`, `ä`, `ö`
- **fr** — `é`, `è`, `à`, `ç`, `ù`
- **de** — `ü`, `ß` (shared `ö/ä` with sv — require both to confirm de)
- **es** — `ñ`, `¿`, `¡`
- **other** — look for `Also ([lang]):` marker

Skip if target-language characters already present in description.

Skip path-based skills: description consists primarily of glob patterns (`**/`, `src/`, file extensions). These trigger on file edits, not words — language triggers add noise.

## 3 — Generate triggers

For each skill needing coverage, understand its domain from the full description. Generate **8–12 trigger phrases** that:

- Match how a native speaker naturally asks for this skill
- Cover both formal and casual registers
- Stay true to the skill's actual domain
- Include action verbs ("debugga", "refaktorera") and question fragments ("förstår inte varför", "hur ska jag")

Append to description using this pattern:
```
[existing description]. Also: "phrase1", "phrase2", ...
```

Use the Edit tool. One skill at a time.

## 4 — Report

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

</process>

<dry_run>
With `--dry-run`: print proposed additions without writing. Confirm before proceeding.

```
[software-design]
  would add: "designa", "refaktorera", "granskning", "arkitektur", ...

Proceed? (y/n)
```
</dry_run>

<idempotency>
Running polyglot twice is safe. Coverage detection on characters prevents double-adding.
If triggers were manually edited between runs, presence of target-language characters still signals coverage.
</idempotency>
