---
description: New project kickoff orchestrator — runs spec → plan → build → test → review → ship in sequence, with human checkpoints between phases
---

Invoke the agent-skills:spec-driven-development and agent-skills:planning-and-task-breakdown skills together as a project kickoff workflow.

`/workflow` is a **sequential lifecycle orchestrator** for starting a new project from scratch. It walks through every phase of the development lifecycle in order, pausing for human confirmation at each checkpoint before proceeding.

## Phase 1 — Spec

Invoke the agent-skills:spec-driven-development skill.

Ask the user clarifying questions to understand:
1. What are we building and who is it for?
2. Core features and acceptance criteria
3. Tech stack preferences and constraints
4. Known boundaries (always do, ask about, never do)

Generate a structured SPEC.md covering: objective, commands/features, project structure, code style, testing strategy, and boundaries.

**Checkpoint:** Present the spec. Ask the user to confirm before proceeding to planning.

## Phase 2 — Plan

Invoke the agent-skills:planning-and-task-breakdown skill.

Using the confirmed SPEC.md:
1. Enter read-only plan mode — analyze the repo, no code changes yet
2. Map the dependency graph between components
3. Slice work vertically (one complete feature path per task)
4. Write tasks with acceptance criteria, verification steps, and size estimates
5. Add explicit checkpoints between phases
6. Save to tasks/plan.md and tasks/todo.md

**Checkpoint:** Present the full task plan. Ask the user to confirm before writing any code.

## Phase 3 — Build

Invoke the agent-skills:incremental-implementation and agent-skills:test-driven-development skills.

For each task in tasks/todo.md:
1. Read the task's acceptance criteria
2. Load relevant context (existing code, patterns, types)
3. Write a failing test (RED)
4. Implement the minimum code to pass (GREEN)
5. Run full test suite — no regressions
6. Run the build — must compile clean
7. Commit with a descriptive message referencing the task
8. Mark the task complete in tasks/todo.md

If any step fails, invoke the agent-skills:debugging-and-error-recovery skill.

**Checkpoint after every 2-3 tasks:** Confirm tests pass, build is clean, feature works end-to-end before continuing.

## Phase 4 — Review

Invoke the agent-skills:code-review-and-quality skill.

Run a five-axis review on all changes since the project started:
- Correctness: logic, edge cases, error handling
- Readability: naming, structure, comments
- Architecture: coupling, cohesion, separation of concerns
- Security: input validation, secrets handling, auth/authz
- Performance: algorithmic complexity, I/O patterns

Fix all Critical findings before proceeding. Present Important findings to the user for a decision.

**Checkpoint:** Confirm all critical issues resolved before shipping.

## Phase 5 — Ship

Invoke the agent-skills:shipping-and-launch skill via `/ship`.

Fan out to three specialist personas in parallel:
- `code-reviewer` — final correctness/quality pass
- `security-auditor` — OWASP Top 10, secrets, auth/authz, CVEs
- `test-engineer` — coverage gaps in happy path, edge cases, error paths

Synthesize a GO / NO-GO decision with a mandatory rollback plan.

## Rules

1. Never skip a phase — each one builds on the previous.
2. Never proceed past a checkpoint without explicit user confirmation.
3. Tasks/todo.md is the single source of truth for progress — update it in real time.
4. If the user jumps in mid-session (e.g. says `/workflow` mid-project), detect the current phase from tasks/todo.md and resume from there.
5. The spec is locked after Phase 1 confirmation — scope creep goes into a separate backlog, not the current workflow.
