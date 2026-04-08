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

## Critical Rules
- **Write directly** — use Write/Edit tools to author the RFC inline. NEVER spawn sub-agents or task agents for document writing.
- **Sub-agents for research only** — Agent tool is allowed exclusively for parallel codebase investigation (Step 4). Never use Agent tool during the writing steps (Steps 6-7).
- **Investigation budget** — spend no more than 10 tool calls exploring the codebase (per agent, if parallel). After that, start writing. Fill knowledge gaps with `[TODO: ...]` markers to resolve with targeted follow-ups.
- **Structured workflow** — the procedure follows a strict sequence: clarify, investigate, analyze, write, validate. Never go back to investigation once writing has started (except for reference validation in Step 8, which is a post-writing verification step, not open-ended exploration).
- **Checkpoint progress** — save investigation findings incrementally to `docs/.rfc-progress/[slug].md`. This prevents losing work on context overflow.
- **Always include Mermaid diagrams** — architecture flows, entity relationships, and sequence diagrams where they clarify the proposal.
- **Always produce a condensed summary** — after the full RFC, create a short-form summary version in the same file (final section).
- **Validate before saving** — when the RFC references existing code, grep-check that every file, function, and entity mentioned actually exists in the codebase (Step 8).

## Procedure

### Step 1: Confirm Inputs, Output Path, and Check for Prior Progress
1. Confirm the proposal topic from the user (feature or refactor).
2. Create a kebab-case slug from the topic.
3. Set output path to `./docs/rfc/[slug].md`.
4. **Check for checkpoint file** at `docs/.rfc-progress/[slug].md`.
   - If a checkpoint exists, read it and present a summary to the user: what was already investigated, decisions made, and where the previous session stopped.
   - Ask the user whether to resume from the checkpoint or start fresh.
   - If resuming, skip already-completed investigation items and proceed from the recorded next steps.
5. If the target RFC file already exists, ask the user before overwriting.

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
Ask concise follow-up questions when required before drafting. Prioritize clarifying these dimensions (in order of importance):
1. Motivation and problem statement.
2. Scope boundaries and explicit non-goals.
3. Stakeholders (users, teams, or systems affected).
4. Constraints and dependencies.
5. Expected impact (product, operational, and technical).

Ask up to 3 focused clarifying questions, then proceed. Each question may address multiple dimensions. For any dimension not clarified by the user's answers, use `[TODO: ...]` markers in the draft and move forward.

### Step 4: Bounded Investigation (When Codebase Context is Needed)
When the RFC requires codebase context (skip this step for purely conceptual proposals):
1. Limit exploration to **10 tool calls maximum** per investigation track (Read, Grep, Glob, Bash).
2. Focus on: relevant data models, existing patterns, API contracts, and event flows.
3. If still missing information after the budget, note gaps as `[TODO: investigate ...]` and move to writing.
4. Do NOT restart investigation during the writing phase.

**Parallel investigation (for multi-service/multi-domain RFCs):**
When the RFC spans multiple services, domains, or codebases, spawn parallel Agent sub-agents for research:
- Each agent gets a specific, bounded research question (e.g., "investigate data models in service-X", "map Kafka event flows for domain-Y").
- Each agent writes its findings to a **separate file**: `docs/.rfc-progress/[slug]-[agent-topic].md` (e.g., `[slug]-data-models.md`, `[slug]-event-flows.md`). This avoids write conflicts between concurrent agents.
- Each agent is limited to 10 tool calls.
- The main session waits for all agents to complete, reads all agent finding files, and merges the results into the main checkpoint file (`docs/.rfc-progress/[slug].md`).
- Example split: Agent 1 investigates domain models and persistence patterns. Agent 2 maps event flows and consumers. Agent 3 analyzes volume/traffic patterns.

**Checkpoint protocol:**
During investigation, save findings incrementally to `docs/.rfc-progress/[slug].md` with the following structure:
```markdown
# RFC Progress: [Topic]
Status: investigating | writing | interrupted | complete
Last updated: [ISO timestamp]

## Investigation Findings
### [Category: e.g., Data Models, Event Flows, API Contracts]
- Finding 1
- Finding 2

## Decisions Made
- Decision 1: [rationale]

## Open Questions
- Question 1

## Next Steps
- [ ] Step description
```
Update the checkpoint file after each significant finding or decision. This file is the handoff document if the session is interrupted.

### Step 5: Evaluate Alternatives and Trade-offs (Mandatory)
1. Capture at least 2 alternatives (including doing nothing when relevant).
2. For each alternative, evaluate:
   - Benefits
   - Drawbacks
   - Risks
   - Why it was or was not selected

### Step 6: Draft RFC Using Template (Mandatory)
1. Read `assets/rfc-template.md`.
2. Keep the template section order unchanged. Additional sections (like `Resumo Executivo` from Step 7) are appended after the template sections, never inserted between them.
3. Fill each section with concrete, verifiable statements.
4. Use clear outcome language (what changes and why), not implementation instructions.
5. For refactors, explicitly describe migration/rollout considerations in Impact.
6. **Include Mermaid diagrams** for:
   - Architecture overview (component/service relationships).
   - Key flows (sequence or flowchart diagrams).
   - Entity relationships when data models are central to the proposal.
7. Use tables for structured comparisons (alternatives, impact matrices).

### Step 7: Add Condensed Summary (Mandatory)
After the full RFC content, append a `## Resumo Executivo` section containing:
1. One-paragraph problem statement.
2. Proposed solution in 2-3 sentences.
3. Key architecture diagram (Mermaid) — simplified version of the main diagram.
4. Top 3 risks or trade-offs.
5. Recommended next steps.

This summary should be self-contained and readable without the full RFC.

### Step 8: Validate Codebase References (When RFC References Existing Code)
Skip this step if the RFC does not reference specific files, functions, or entities in the codebase (e.g., a purely conceptual new-feature RFC).

This is a **targeted verification step**, not open-ended investigation. Do not explore new areas of the codebase — only confirm that what the RFC already claims exists is accurate.

1. Extract all file paths, function names, service names, entity names, and event/topic names mentioned in the RFC.
2. For each reference, run a targeted Grep or Glob to confirm it exists in the codebase.
3. For each reference that does NOT exist:
   - If it was a typo or outdated name, fix it in the RFC.
   - If it cannot be verified (e.g., external service), add a note: `[Unverified: ...]`.
4. Log validation results in the checkpoint file under a `## Validation` section.

### Step 9: Validate RFC Quality (Mandatory)
Before saving, verify:
1. Motivation is problem-first and specific.
2. Scope and out-of-scope are explicit and testable.
3. Impact addresses compatibility and operational implications.
4. Alternatives section includes meaningful trade-offs.
5. Open questions or unresolved decisions are clearly listed.
6. Language is concise and free of implementation-level detail.
7. Mermaid diagrams are present, syntactically correct, and enhance understanding.
8. Condensed summary is present and self-contained.
9. All `[TODO: ...]` markers are listed in the open questions section.
10. All codebase references passed validation (Step 8).

### Step 10: Save and Report
1. Create directory `./docs/rfc/` when missing.
2. Save as `./docs/rfc/[slug].md`.
3. Update the checkpoint file status to `complete`.
4. Report:
   - Final file path
   - One-paragraph summary
   - Open questions that still need stakeholder feedback
   - List of any remaining `[TODO: ...]` markers
   - Validation results (how many references checked, any unverified)

## Decision Points
- If proposal type is unclear: ask whether the main goal is delivering behavior or improving existing architecture.
- If motivation is weak: pause and ask for measurable problem indicators.
- If alternatives are missing: do not finalize; add at least two.
- If template file is unavailable: halt and report missing dependency.
- If investigation budget is exhausted and gaps remain: stop investigating, start writing with `[TODO: ...]` markers.
- If RFC spans multiple services or domains: use parallel investigation agents (Step 4).
- If a checkpoint file exists for the slug: ask the user whether to resume or start fresh (Step 1).
- **If the conversation is getting long** (approaching context limits): immediately save a comprehensive handoff to the checkpoint file with status `interrupted`, all findings so far, decisions made, and explicit next steps. Inform the user that progress was saved and they can resume in a new session.

## Core Principles
- **Write early, refine iteratively** — a draft with TODOs beats a session that produces nothing.
- Clarify before drafting, but don't block on perfect information.
- Prefer measurable statements over vague language.
- RFC captures intent, boundaries, and trade-offs.
- New feature and refactor RFCs follow the same structure but different emphasis.
- Diagrams are first-class content, not optional decoration.
- **Ground claims in code** — every architectural reference in the RFC should be verifiable against the codebase.
- **Never lose progress** — checkpoint findings incrementally so context overflow doesn't waste work.

## Quality Checklist
- [ ] Proposal type classified (`new-feature`, `refactor`, or `mixed`).
- [ ] Checkpoint file checked for prior progress (Step 1).
- [ ] Core ambiguity resolved before drafting (max 3 clarifying questions).
- [ ] Investigation phase stayed within 10 tool-call budget per agent (if Step 4 applied).
- [ ] Parallel agents used for multi-service RFCs (if applicable; N/A for single-service).
- [ ] Investigation findings saved to checkpoint file incrementally (if Step 4 applied).
- [ ] At least two alternatives documented with trade-offs.
- [ ] RFC generated from `assets/rfc-template.md` without section reordering.
- [ ] Scope and out-of-scope are explicit.
- [ ] Impact and compatibility implications are documented.
- [ ] Mermaid diagrams included (architecture, flows, or entity relationships).
- [ ] Condensed summary (`Resumo Executivo`) is present and self-contained.
- [ ] Codebase references validated via grep-check (if Step 8 applied; N/A for conceptual RFCs).
- [ ] All `[TODO: ...]` markers tracked in open questions.
- [ ] File saved to `./docs/rfc/[slug].md`.
- [ ] Checkpoint file updated to `complete` status.
- [ ] Final path, summary, open questions, and validation results reported.
- [ ] RFC was written directly with Write/Edit tools (no sub-agents for writing).

## Error Handling
- If context is insufficient, ask targeted clarification questions (max 3) and pause drafting.
- If `assets/rfc-template.md` is missing, report the issue and stop.
- If output file exists, request overwrite confirmation.
- If the proposal mixes feature and refactor goals but priorities conflict, ask the user to choose a primary objective before finalizing.
- If investigation runs over budget, stop immediately and proceed to writing with `[TODO: ...]` markers for gaps.
- If a parallel investigation agent fails or times out, proceed with findings from the other agents and mark the failed area as `[TODO: ...]`.
- If approaching context limits, immediately execute handoff protocol: save all progress to checkpoint file with status `interrupted` and inform the user. Do not attempt to finish the RFC in a degraded state — a clean handoff is better than a rushed, incomplete document.
- If resuming from a checkpoint, validate that referenced files/paths in the checkpoint still exist before relying on prior findings.