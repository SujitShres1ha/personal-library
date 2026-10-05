---
description: Add an entry to the dev log
argument-hint: <progress|learned|problem|decision> <what happened>
---

Add an entry to `docs/dev-log.md`.

Input: $ARGUMENTS

1. The first word is the type: progress, learned, problem or decision. If it is
   missing or unclear, infer it from the text and tell me which you chose.
2. Work out which roadmap step this relates to (from `docs/roadmap.md` and what
   we have been working on). Use "General" if none fits.
3. Under a heading for today's date (`## YYYY-MM-DD`, created if missing, at
   the end of the file), append one line in this format:
   `- **Type** (Step name): text`
4. Keep my wording. Only fix typos and make it a complete sentence.
5. Show me the line you added. Do not change anything else.
