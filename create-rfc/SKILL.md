---
name: create-rfc
description: 'Create an RFC for a new feature or refactor using a structured workflow: scope, clarify, evaluate alternatives, draft from template, and validate quality. Use when the user asks to write an RFC, formalize a proposal, or document a refactoring decision. Do not use for implementation task breakdowns or detailed tech specs.'
argument-hint: 'Feature or refactor name (for example: "user profile revamp" or "payments service refactor")'
---

# RFC Creation

## When To Use
- Writing an RFC for a new feature.
- Writing an RFC for a refactor or architecture cleanup.
- Aligning stakeholders on motivation, impact, alternatives, and out-of-scope.

## Do Not Use
- Writing implementation plans, task lists, or sprint breakdowns.
- Writing low-level technical specs with code-level design.

## Procedure

### Step 1: Confirm Inputs and Output Path
1. Confirm the proposal topic from the user (feature or refactor).
2. Create a kebab-case slug from the topic.
3. Set output path to `./docs/rfc/[proposal-slug].md`.
4. If the target file already exists, ask the user before overwriting.

### Step 2: Determine RFC Type and Branch
1. Classify the proposal as one of:
   - `new-feature`
   - `refactor`
   - `mixed` (both)
2. Use branch-specific focus:
   - `new-feature`: user value, expected behavior, adoption impact.
   - `refactor`: current pain points, maintainability/reliability gains, migration and compatibility risks.
   - `mixed`: include both value delivery and codebase health rationale.

### Step 3: Clarify Missing Information (Mandatory)
Ask concise follow-up questions when required before drafting. Cover these dimensions:
- Motivation and problem statement.
- Stakeholders (users, teams, or systems affected).
- Scope boundaries and explicit non-goals.
- Constraints and dependencies.
- Expected impact (product, operational, and technical).

Do not draft until core ambiguities are resolved.

### Step 4: Evaluate Alternatives and Trade-offs (Mandatory)
1. Capture at least 2 alternatives (including doing nothing when relevant).
2. For each alternative, evaluate:
   - Benefits
   - Drawbacks
   - Risks
   - Why it was or was not selected

### Step 5: Draft RFC Using Template (Mandatory)
1. Read `assets/rfc-template.md`.
2. Keep the template section order unchanged.
3. Fill each section with concrete, verifiable statements.
4. Use clear outcome language (what changes and why), not implementation instructions.
5. For refactors, explicitly describe migration/rollout considerations in Impact.
6. Use mermaid diagrams or tables if they clarify complex relationships, flows or trade-offs.

### Step 6: Validate RFC Quality (Mandatory)
Before saving, verify:
1. Motivation is problem-first and specific.
2. Scope and out-of-scope are explicit and testable.
3. Impact addresses compatibility and operational implications.
4. Alternatives section includes meaningful trade-offs.
5. Open questions or unresolved decisions are clearly listed.
6. Language is concise and free of implementation-level detail.
7. Diagrams and tables are clear, accurate, and enhance understanding.

### Step 7: Save and Report
1. Create directory `./docs/rfc/` when missing.
2. Save as `./docs/rfc/[proposal-slug].md`.
3. Report:
   - Final file path
   - One-paragraph summary
   - Open questions that still need stakeholder feedback

## Decision Points
- If proposal type is unclear: ask whether the main goal is delivering behavior or improving existing architecture.
- If motivation is weak: pause and ask for measurable problem indicators.
- If alternatives are missing: do not finalize; add at least two.
- If template file is unavailable: halt and report missing dependency.

## Core Principles
- Clarify before drafting.
- Prefer measurable statements over vague language.
- RFC captures intent, boundaries, and trade-offs.
- New feature and refactor RFCs follow the same structure but different emphasis.

## Quality Checklist
- [ ] Proposal type classified (`new-feature`, `refactor`, or `mixed`).
- [ ] Core ambiguity resolved before drafting.
- [ ] At least two alternatives documented with trade-offs.
- [ ] RFC generated from `assets/rfc-template.md` without section reordering.
- [ ] Scope and out-of-scope are explicit.
- [ ] Impact and compatibility implications are documented.
- [ ] File saved to `./docs/rfc/[proposal-slug].md`.
- [ ] Final path, summary, and open questions reported.

## Error Handling
- If context is insufficient, ask targeted clarification questions and pause drafting.
- If `assets/rfc-template.md` is missing, report the issue and stop.
- If output file exists, request overwrite confirmation.
- If the proposal mixes feature and refactor goals but priorities conflict, ask the user to choose a primary objective before finalizing.