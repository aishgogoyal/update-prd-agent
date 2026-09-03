---
name: prd-feedback-loop
description: Update an EXISTING PRD with new context that has emerged since it was last edited — new meetings, standup notes, tickets, design docs, or Slack discussion. Use this whenever the user asks to "update," "refresh," or "sync" a PRD that already exists, or references a PRD that has drifted from recent decisions. This is distinct from expand-prd-writer, which drafts a brand-new PRD from Explore-phase materials for an idea that has never had one. Trigger this skill whenever the user provides an existing PRD link/doc PLUS new source material and asks what should change, even if they don't use the word "update" explicitly (e.g. "does this PRD reflect what we decided yesterday", "sync this PRD with the latest standup").
---

# PRD Feedback Loop — Update Agent

## Purpose

Idea→PRD tools (like `expand-prd-writer`) solve the *creation* problem: turning raw Explore-phase material into a first PRD draft. But PRDs drift after that point — decisions get made in standups, tickets, and design docs that never make it back into the document. This skill solves the *drift* problem: given an existing PRD and a bounded set of new context, produce an accurate, traceable update — not a rewrite.

## Core Principle: Blank, Not Hallucinate (inherited, non-negotiable)

Every proposed change must trace back to a specific line or decision in the *new* source material. If the new context is ambiguous, silent, or only tangentially related to a section of the PRD, **do not touch that section**. Leaving something unchanged because the evidence doesn't support a change is a correct outcome, not a failure.

This also means: do not resolve existing Open Items in the PRD unless the new context explicitly resolves them. A tangential mention is not resolution.

## Inputs Required

Before running, confirm you have:
1. **The existing PRD** (full current version, not a summary)
2. **A bounded set of new source material** — e.g. a standup transcript, specific tickets, an in-progress design doc. Do NOT go pull additional unrelated context on your own initiative; use only what's been handed to you, unless the user asks you to search further.
3. Clarity on **what time window counts as "new"** — i.e., what was the PRD's last-edited date, so you know what predates it vs. postdates it.

If any of these are missing or ambiguous, ask before proceeding rather than guessing scope.

## Process

1. **Read the existing PRD in full.** Note its structure (sections, Open Items, Definition of Done, etc.) — the update should slot into this structure, not restructure it.
2. **Read each new source individually.** For each one, extract only what is:
   - Genuinely new (postdates the PRD's last edit)
   - Specific enough to act on (a decision, a changed number, a named owner, a scope change — not a vague mention)
3. **Map each extracted item to a specific PRD section.** Three possible outcome per item, not two:
   - Maps cleanly to an existing section → propose the edit there.
   - Doesn't map to any section AND is a genuinely open/unresolved question → new **Open Item**.
   - Doesn't map to any section BUT the team has already decided it (it's just informational, not a question for this PRD to resolve) → new **Decisions Made** entry (see below). Don't force these into Open Items — an Open Item implies something this PRD needs to resolve; a settled decision from a standup/ticket that just doesn't touch this document's scope is not that.
4. **Draft the update as a diff, not a rewrite.** For each proposed change: quote/cite the specific new-source line that justifies it, state the proposed PRD edit, and note which section it affects.
5. **Do not touch anything without a traceable justification.** Silence in the new material about a given PRD section means that section is untouched.
6. **Flag conflicts explicitly.** If new material contradicts something in the existing PRD (e.g. a superseded technical approach), surface this clearly rather than silently overwriting — this is a decision point for the human reviewer, not something to resolve unilaterally. This includes contradictions within a single new source (e.g. a design doc's own "Resolved" section disagreeing with its own later implementation notes) — flag these too, don't silently pick a side.

### The PRD draft must be 100% complete — no placeholders, no elisions
- Reproduce every unchanged section **verbatim, in full**. Never write `(unchanged)`, `...`, "see original", or any other shorthand standing in for real content — the reviewer needs a document they can immediately copy/paste/publish, not one they have to go reassemble against the original.
- Every single edit, flag, or addition must be visually distinct from the surrounding unchanged text, inline, at the exact point it applies — not collected into a separate section elsewhere in the document.
  - **When the destination is Confluence:** use real colored panels via `data-type="panel-warning|panel-error|panel-success|panel-note"` (warning/error = conflicts and contradictions needing a decision; success/note = confirmations, new Open Items, informational flags). Do not use blockquotes or asterisked parentheticals — they render as indistinguishable prose in Confluence.
  - **When the destination is plain markdown/chat:** blockquotes with a bold label (`> ⚠️ **CONFLICT FLAGGED**...`) are the fallback, but say plainly that real color only happens once it's in Confluence.
- Update the PRD's own **History** field with a dated entry for this pass, naming the sources reviewed and summarizing what changed (conflicts flagged / Open Items added) — don't leave the document's own changelog stale.

## Decisions Made section
Add a `## Decisions Made` section (distinct from Open Items) for anything from the new material that is genuinely settled elsewhere but doesn't map to an existing PRD section. Format it as a table, not prose paragraphs:

`Decision | Source | Date Made`

Each row: the decision stated plainly (bold lead phrase + one sentence), the specific source (standup section name, ticket ID, doc section), and the date it was made (use the source's own date — e.g. the standup date — not today's date). If a decision's applicability to *this* PRD is itself uncertain (e.g. a UI change planned for a related-but-different flow), say so in the decision cell and cross-reference the relevant Open Item — don't silently assume it applies.

## Publishing

If the user gives you a target location (a Confluence page, a doc link) and asks you to put the draft there, do it directly — this skill is not limited to producing a draft for manual copy-paste. Fetch the destination first if it already has content, preserve anything unrelated to this update, and confirm the destination afterward (link back to it) rather than re-pasting the whole document again in chat.

## What This Skill Does NOT Do

- Does not create a PRD from scratch (use `expand-prd-writer` for that)
- Does not resolve Open Items unless the new context explicitly does so
- Does not go find additional context beyond what it's given, unless explicitly asked to search further
- Does not summarize or truncate unchanged sections of the PRD under any circumstances, even for a long document — completeness over brevity, always
