---
name: polyglot
description: "Audit and improve Claude Code skill activation."
when_to_use: "Use this skill when skills don't trigger — adds multilingual trigger phrases for skills missing your language, and flags skills with no activation guidance. Triggers: \"polyglot\", \"localize skills\", \"skills don't activate\", \"add triggers\", \"skills don't trigger in [language]\", \"add [language] to my skills\"."
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

For each SKILL.md, read the `when_to_use` frontmatter field first. Fall back to `description` if `when_to_use` is absent.

Language markers:
- **sv** — `å`, `ä`, `ö`
- **fr** — `é`, `è`, `à`, `ç`, `ù`
- **de** — `ü`, `ß`
- **es** — `ñ`, `¿`, `¡`
- **other** — look for `Also ([lang]):` marker

Skip if target-language characters already present in the activation field.

Skip path-based skills: activation field consists primarily of glob patterns (`**/`, `src/`, file extensions). These trigger on file edits, not words — language triggers add noise.

## 3 — Generate triggers

Count the existing trigger phrases. Generate **the same number** in the target language — no more, no fewer. Match density, don't bloat.

Trigger phrases should:
- Match how a native speaker naturally asks for this skill
- Cover both formal and casual registers
- Stay true to the skill's actual domain
- Include action verbs and question fragments where natural

**Write target**: always `when_to_use`. If the skill has no `when_to_use` field, create one. If triggers currently live only in `description`, leave `description` unchanged and add `when_to_use` instead.

Append to `when_to_use` using this pattern:
```
[existing when_to_use content]. Also: "phrase1", "phrase2", ...
```

If creating `when_to_use` from scratch (skill had no activation field), extract any existing "Use when" clause from `description` as the base, then append the new-language triggers.

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

After the language pass, scan every SKILL.md (not just the ones updated) and check whether any of these activation signals exist:

- `description` contains "use when", "use this skill when", or "trigger" (case-insensitive)
- a `when_to_use` frontmatter field is present

Skills that pass none of these checks lack reliable activation guidance.

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
