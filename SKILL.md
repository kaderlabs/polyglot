---
name: polyglot
description: Add multilingual trigger phrases to Claude Code skills so they activate when you write in any language. Use when skills don't trigger because you wrote in a non-English language. Triggers: "polyglot", "add triggers", "localize skills", "skills don't trigger in [language]", "add [language] to my skills".
---

<objective>
Scan your Claude Code skill library and append native-language trigger phrases to any skill whose description only covers English. Skills activate naturally when you think and write in your preferred language — without rewriting the skills themselves.

Language is required. No default.
</objective>

<invocation>
- `/polyglot sv` — add Swedish triggers to all skills missing them
- `/polyglot fr` — French
- `/polyglot sv --dry-run` — preview without writing
- `/polyglot sv --skill my-skill` — single skill only

If no language is given, ask: "Which language? (e.g. sv, fr, de, es)"
</invocation>

<process>

## 1 — Locate skills

```bash
find ~/.claude/skills -name "SKILL.md" 2>/dev/null
```

If `--skill <name>` is passed, filter to that skill only.

## 2 — Detect coverage

For each SKILL.md, read the `description` frontmatter field.

Language markers:
- **sv** — `å`, `ä`, `ö`
- **fr** — `é`, `è`, `à`, `ç`, `ù`
- **de** — `ü`, `ß`
- **es** — `ñ`, `¿`, `¡`
- **other** — look for `Also ([lang]):` marker

Skip if target-language characters already present in description.

Skip path-based skills: description consists primarily of glob patterns (`**/`, `src/`, file extensions). These trigger on file edits, not words — language triggers add noise.

## 3 — Generate triggers

Count the existing trigger phrases in the description. Generate **the same number** in the target language — no more, no fewer. Match density, don't bloat.

Trigger phrases should:
- Match how a native speaker naturally asks for this skill
- Cover both formal and casual registers
- Stay true to the skill's actual domain
- Include action verbs and question fragments where natural

Append to description using this pattern:
```
[existing description]. Also: "phrase1", "phrase2", ...
```

Use the Edit tool. One skill at a time.

## 4 — Report

Only list skills that were actually updated:

```
polyglot — sv · 2026-05-26
──────────────────────────────────────
✓  my-skill      +4 triggers
✓  other-skill   +6 triggers
──────────────────────────────────────
2 updated
```

## 5 — Check for missing "Use when"

After the language pass, scan every SKILL.md (not just the ones updated) and check whether the `description` field contains "use when" (case-insensitive) or whether a `when_to_use` frontmatter field exists.

If any skills are missing both, append a notice to the report:

```
──────────────────────────────────────
⚠  Skills with no "Use when" clause:
   lint, test, frontend-design
   These won't auto-activate reliably.
   Add "Use when" descriptions? (y/n)
──────────────────────────────────────
```

If the user answers **y**, draft a `Use when:` clause for each affected skill based on its name and existing description body. Present each as a one-line proposal and ask for confirmation before writing.

Do not add "Use when" clauses to path-based skills (those already have `paths` frontmatter for conditional activation).

</process>

<dry_run>
With `--dry-run`: print proposed additions without writing. Confirm before proceeding.

```
[my-skill]
  would add: "phrase1", "phrase2", ...

Proceed? (y/n)
```
</dry_run>

<idempotency>
Running polyglot twice is safe. Coverage detection on characters prevents double-adding.
</idempotency>
