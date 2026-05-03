---
name: skill-validator
description: >
  Audits an existing SKILL.md file for structural issues, missing required fields, weak triggering
  descriptions, anti-patterns, and marketplace-readiness. Use this skill whenever a user says
  "check my skill", "review this SKILL.md", "audit my skill", "is this skill ready to publish",
  "why isn't my skill triggering", or pastes a SKILL.md file and asks for feedback.
  Also triggers when a user shares a skill file that looks incomplete, has a vague description,
  or is behaving unexpectedly. Returns a scored report with specific, actionable fixes for every
  issue found. Always use this skill before publishing or sharing a skill file.
---

# Skill Validator — Anvil & Code

Audits SKILL.md files for structural correctness, triggering strength, and marketplace readiness.
Produces a scored report with every issue categorized by severity and a specific fix for each.

---

## How to Use

Paste a SKILL.md file (or its contents) and say "validate this" or "audit this skill."
The validator will scan it, score it, and return a full report.

---

## Validation Checklist

Run every check below. Report every finding — do not silently skip checks that pass.

### YAML Frontmatter (Critical — failures here make the skill invisible)

| Check | Pass | Fail |
|-------|------|------|
| `name` field present | ✅ | ❌ CRITICAL: Skill won't load |
| `name` in kebab-case | ✅ | ⚠️ WARNING: May cause ID conflicts |
| `description` field present | ✅ | ❌ CRITICAL: Skill will never trigger |
| `description` is 60–120 words | ✅ | ⚠️ WARNING: Under 60 = under-triggers; Over 200 = diluted signal |
| `description` starts with an action verb | ✅ | ⚠️ WARNING: Passive descriptions under-trigger by ~40% |
| `description` contains 3+ trigger phrases | ✅ | ⚠️ WARNING: Skill won't fire on real user inputs |
| `description` names input AND output format | ✅ | ⚠️ WARNING: Claude can't match skill to context |
| No placeholder text in YAML | ✅ | ❌ CRITICAL: Placeholder text breaks triggering |

### Body Structure (Important — missing sections produce unreliable output)

| Check | Pass | Fail |
|-------|------|------|
| Has a clear purpose statement in first 3 lines | ✅ | ⚠️ WARNING |
| Has an output format section | ✅ | ⚠️ WARNING: Claude will invent a format |
| Has an anti-patterns section | ✅ | ⚠️ INFO: Skill will occasionally produce bad output |
| Has at least 2 test cases | ✅ | ⚠️ INFO: No way to verify skill works without tests |
| No placeholder text ([TODO], [INSERT], etc.) | ✅ | ❌ CRITICAL: Incomplete skill |
| Under 500 lines total | ✅ | ⚠️ WARNING: Large skills load slowly and may be partially ignored |
| No instructions that contradict each other | ✅ | ❌ CRITICAL: Claude will behave unpredictably |

### Triggering Quality (Determines whether the skill actually fires)

Score the description on each dimension (1–3):

**Specificity** — Does it name specific inputs, outputs, and user phrases?
- 3: Names exact input format, output format, and 3+ trigger phrases
- 2: Names some inputs/outputs but trigger phrases are vague
- 1: Generic ("helps with X") — will not trigger reliably

**Action orientation** — Does it start with what Claude will DO?
- 3: Starts with verb: "Generates...", "Audits...", "Converts..."
- 2: Subject-first but active: "This skill generates..."
- 1: Passive or noun-first: "A skill for..." or "Can be used to..."

**Coverage** — Does it cover the real ways users will ask for this?
- 3: Includes formal request, casual request, and adjacent use case
- 2: Covers formal request only
- 1: Only matches one exact phrasing

**Total triggering score: X/9**
- 8–9: Strong — will trigger reliably
- 5–7: Moderate — will miss some valid use cases
- Below 5: Weak — needs a rewrite before publishing

### Anti-Pattern Detection

Flag any of the following if present:

- ❌ Unicode bullets (•, -, –) in YAML values — breaks YAML parsing
- ❌ HTML tags in the skill body — not rendered, creates noise
- ❌ Hardcoded file paths that don't exist in the skill's directory
- ❌ References to tools or MCPs not listed in a `compatibility` section
- ❌ Instructions that say "always" and also say "never" about the same thing
- ❌ Empty sections with only a heading
- ❌ Test cases with no expected output described

---

## Output Format

Return the report in this exact structure:

```
## Skill Validation Report: [skill name]

**Overall Score: [X/10]**
[One sentence verdict: Ready to publish / Needs minor fixes / Needs major revision]

---

### Critical Issues (fix before using)
[List each CRITICAL finding with: what's wrong, where it is, exact fix]

### Warnings (fix before publishing)
[List each WARNING finding with: what's wrong, where it is, recommended fix]

### Info (optional improvements)
[List each INFO finding with: what it is and why it's worth fixing]

### Triggering Score: [X/9]
[Specificity: X/3 — one-line reason]
[Action orientation: X/3 — one-line reason]
[Coverage: X/3 — one-line reason]

### What's Working Well
[2–3 things the skill does right — be specific]

### Recommended Next Step
[Single most important thing to fix first]
```

---

## Scoring Rubric (Overall 0–10)

Start at 10. Deduct:
- 3 points per CRITICAL issue
- 1 point per WARNING
- 0.5 points per INFO
- Floor at 0

A score of 7+ is publishable. Below 7, the skill needs revision before listing.

---

## Anti-Patterns

- Never give a passing score to a skill with any CRITICAL issues
- Never skip the triggering score — it's the most important single metric
- Never suggest fixes without explaining why the current version is wrong
- Never give vague feedback like "improve the description" — always give the specific rewrite

---

## Test Cases

**Test 1 — Strong skill**
Input: A well-formed SKILL.md with complete frontmatter, clear output format, 3+ test cases
Expected: Score 8–10, few or no warnings, positive "What's Working" section

**Test 2 — Weak description**
Input: A SKILL.md where the description is 15 words and starts with "A skill for..."
Expected: Triggering score 2–3/9, CRITICAL issue flagged for description length and action verb

**Test 3 — Placeholder text**
Input: A SKILL.md with [TODO: add examples here] in the body
Expected: CRITICAL issue flagged, score deducted, exact location of placeholder cited
