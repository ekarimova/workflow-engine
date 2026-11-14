# todo: where the work stands

Kept up to date by everyone who works on the engine (human or agent): after each task, story, decision or deployment. To start Phase 0: `claude`, then `/kickoff-epic0`; to continue: `/next` (`kickoff-prompt.md`). Newest entries first. The plan: `ai-docs/plan.md`.

Last update: 2026-11-14, main session (Claude): PO decisions D-09 to D-13 recorded; target for 20 November = wish to reviewed workflow.

## Current position

| Level | Item | State |
|---|---|---|
| Goal | [G-01 Technical setup](ai-docs/goals/G-01.md) | Planned (due 2026-11-20) |
| Epic | [E-00 Technical project setup](ai-docs/epics/E-00.md) | Planned; kickoff plan approved 2026-11-13, changed 2026-11-14 (D-09 to D-12) |
| Story | [US-00.01 Repository and layout](ai-docs/stories/US-00.01.md) | Draft (Definition of Ready not checked) |
| Task | [T-00.01.2](ai-docs/tasks/T-00.01.2.md) (human: push `main` to GitHub, pointer branches, branch protection, collaborators), then [T-00.01.1](ai-docs/tasks/T-00.01.1.md) | open |

The Git repository exists (branch `main`, commits `99c096b`, `d014303`); `origin` = `github.com/ekarimova/workflow-engine` (public), where `main` is at `99c096b`. No product code exists: `backend/`, `frontend/`, `api/`, `ci/`, `infrastructure/` are not created. The code skeleton waits in `ai-docs/skeleton/`.

**Next action:** human: merge the pull request with the plan changes of 2026-11-14 on GitHub, set the branch protection and add the collaborators (T-00.01.2); then the Epic 0 developer runs `/kickoff-epic0` (starts US-00.01).

## Decisions needed (human: Product Owner and approving Architect)

| ID | Decision | Blocks | Recommendation | State |
|---|---|---|---|---|
| Public | `ekarimova/workflow-engine` is public; keep it public (course, other developers) or make it private until the presentation | nothing (no secret is in it) | Your choice; T-00.01.2 step 5 | open |
| OI-2, 3, 5, 8, 9 | Open issues of `requirements.md` Appendix A (presentation access, audit report in the ZIP, cost limits, MCP door, model per agent) | E-04, E-06 | Decide when the epic is planned | open |
| OI-4 | Default harness: Claude Code, opencode or both | E-06 | Answered by the spike US-00.S1 | open |

## Decisions taken

| ID | Decision | Date | Recorded in |
|---|---|---|---|
| D-01 | Everything on the local machine with Docker until the presentation; maybe never remote. Dev, test, prod are compose projects on it. A later host name may be amberneurons-engine (.com, .at or .ai) | 2026-11-13 | ADR-041 (`architecture.md` 5), `CLAUDE.md` 9, `ai-docs/plan.md` |
| D-02 | `workflow-engine` is its own Git repository and stays inside `~/DEV`; `~/DEV` ignores it (`~/DEV/.gitignore`, its staged entries of this folder removed from the index) | 2026-11-13 | `git init -b main`; first commit `99c096b` |
| D-03 | Use what is installed: Java 21.0.12, Gradle wrapper (Kotlin DSL), Node 24.14.1, npm 11 (as in `ci-cd.md` 4); install only what a step needs | 2026-11-13 | `ai-docs/stories/US-00.02.md`, `CLAUDE.md` 9 |
| D-05 | Every merged story to local and dev; every finished epic promoted to test and prod (PO approval for prod), all local | 2026-11-13 | ADR-041; story US-00.14 added to E-00 |
| D-06 | Runs do not ask the client, except in the very rare case of a defect that is not fixable; when tokens run out (rate limit or credit) the run waits and continues instead of failing | 2026-11-13 | `requirements.md` 0.4: FR-EXE-04, FR-EXE-07, FR-EXE-08, new FR-EXE-11; E-06 (US-06.10) |
| D-07 | Skeleton domain: `status` (`workflow.md` 9.2); replaced by the first business story (WFL-074) | 2026-11-13 | US-00.03 |
| D-08 | Requirements and project documents are generated after the answers (screen 3) and updated from the contract at confirmation | 2026-11-13 | `requirements.md` 0.4: FR-DOC-01, FR-DOC-02; E-03 (US-03.04) |
| D-12 | Other developers work on the engine from Epic 0 on, each with their own Claude login and their own Anthropic key (local `.env`, entered on the page at run time); all through pull requests | 2026-11-14 | US-00.09 (in the 20 Nov scope), `kickoff-prompt.md` 1 and 5 |
| D-11 | AI from the very beginning; MVP to be shown to the client: new US-00.15 (AI connection check with the developer's own key) in Epic 0; in G-02 the AI stories go first (demo path) | 2026-11-14 | `ai-docs/stories/US-00.15.md`, `ai-docs/plan.md` (MVP for the client), E-01 |
| D-10 | CON-01: no example workflow in the product for now; concentrate on the engine. An example workflow may be used as test data where a test needs one | 2026-11-14 | US-00.05 (out of scope) |
| D-09 | GitHub: use the existing repository `github.com/ekarimova/workflow-engine` (already `origin`); pull-request pipeline in Epic 0 | 2026-11-14 | US-00.01, US-00.09, T-00.01.2 |
| D-14 | Agents commit and push on story or docs branches and open the pull request; the human (or another developer) merges on GitHub; never a push to `main`. `/next` (skill `next`) is the one command to continue the work | 2026-11-14 | `kickoff-prompt.md`, `.claude/skills/next/SKILL.md` |
| D-15 | The owner does not build Phase 0: another developer takes over Epic 0 with `/kickoff-epic0`, then `/next`. The owner stays Product Owner (decisions, pull-request review and merge) | 2026-11-14 | `kickoff-prompt.md`, `.claude/skills/kickoff-epic0/SKILL.md` |
| D-13 | Target for 20 November: the path from prompt to workflow generation and review before execute works end to end with Claude (BPMN level 1 up to Freeze contract), thin versions; execution prepared, not built. Business stories start after US-00.10; US-00.13, 11, 14, 07, 12, S1 and the E-00 gate after 20 Nov | 2026-11-14 | `ai-docs/plan.md` (Target for 20 November), E-00 to E-04, E-06 |

The Word master of the requirements (`ai-docs/word/Workflow-Engine-Requirements-Specification.docx`) does not yet contain the 0.4 changes (human).

## Next (in order)

Kickoff plan of 2026-11-13, changed by the PO decisions D-09 to D-13 of 2026-11-14. Each story: `/start-story` → tasks → `ci/run all-local` → `/team-verification` → squash-merge (human) → `ci/run deploy dev` → update this file.

1. Until 20 November, Epic 0 (setup): US-00.01 → US-00.02 → US-00.03 → US-00.04 → US-00.05 → US-00.15 (AI check, own key) → US-00.06 → US-00.08 → US-00.10 (local dev); US-00.09 (GitHub pull-request pipeline, onboarding) in parallel by DevOps.
2. Until 20 November, from the wish to the reviewed workflow (D-13, `ai-docs/plan.md`): US-01.01 → US-01.05 → US-01.07 → US-02.01 → US-02.02 → US-03.01 → US-04.01 → US-04.02 (first BPMN-like diagram) → US-04.06 (review before execute, Run disabled) → US-04.07 (contract snapshot). Execution only prepared.
3. After 20 November: US-00.13, US-00.11, US-00.14, US-00.07, US-00.12, US-00.S1; E-00 gate (`/audit-report AP2 E-00`, promote dev → test → prod, G-01 sign-off); remaining stories of E-01 to E-04; E-05; E-06 execution; E-07.

## In progress

- nothing

## Done

- 2026-11-14 `kickoff-prompt.md` shortened to `/next`; new skill `next`; `/start-story` gate follows D-13; skill `kickoff-epic0` rewritten as the Phase 0 kickoff for the developer who takes over Epic 0 (D-15).
- 2026-11-14 PO decision D-13: target for 20 November = wish → questions → requirements → workflow → review → contract, thin, with Claude; `ai-docs/plan.md`, E-00 to E-04, E-06 updated.
- 2026-11-14 PO decisions D-09 to D-12: new story US-00.15 with tasks T-00.15.1 to 4; US-00.09 into the 20 Nov scope with onboarding criteria; US-00.01, US-00.05, T-00.01.2, T-00.09.1, E-00, E-01, `ai-docs/plan.md` and `kickoff-prompt.md` updated.
- 2026-11-14 Next list and the "20 Nov" column of `ai-docs/epics/E-00.md` set to the approved order. Cards US-00.05 and US-00.13 wait for the CON-01 answer.
- 2026-11-13 Kickoff plan approved. D-04 applied by the human (guard path rules, 48 self-test cases pass, script deleted; commit `d014303`).

- 2026-11-13 PO decisions D-01 to D-08 recorded (ADR-041, `requirements.md` 0.4, `CLAUDE.md` 9); own Git repository initialised; E-00 adapted (US-00.10 local dev, new US-00.14 local test and prod, US-00.09 only with GitHub); guard update script prepared and tested on a copy (48 cases pass).
- 2026-11-13 Plan written: `ai-docs/plan.md`, goals G-01 to G-04, epics E-00 to E-10, the story cards and task specs of E-00; `kickoff-prompt.md` and this file at the root.
- 2026-11-13 Documents restructured: one `ai-docs/README.md`; rule documents as skills in `.claude/skills/<area>/`; skeleton in `ai-docs/skeleton/`; new skills `screen-build`, `bpmn-model`, `find-rule`, `docs-check`, `agent-rules`, `project-learnings`, `kickoff-epic0`.

## Deployments

All environments are on the local machine (ADR-041).

| Environment | Build id | Story or epic | Date | Result |
|---|---|---|---|---|
| local | — | — | — | not available (no code yet) |
| dev | — | — | — | not available until US-00.10 |
| test | — | — | — | not available until US-00.14 |
| prod | — | — | — | not available until US-00.14 |
