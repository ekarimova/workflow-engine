---
name: project-learnings
description: "Lessons learned on the workflow engine repository: where documents live after the 13 November 2026 restructure, read-only files, guard hook behaviour, known gaps in the documents and pending human actions. Use before moving, renaming or merging documents or skills, before changing .claude/, when a guard hook blocks a call unexpectedly, and at the start of Epic 0 planning."
---

# Project learnings

Short, dated entries. Add a new entry at the top of its section when a story, audit or session teaches something that the next agent would otherwise rediscover (`agents.md` 10, `workflow.md` lessons). A lesson that changes a rule becomes a proposal to the owner of that document (WFL-094); this file does not override the documents.

## Where things are (13 November 2026)

- Product documents in `ai-docs/` (index: `ai-docs/README.md`, the only README). The 13 rule documents are skills: `.claude/skills/<area>/<area>.md` with a short `SKILL.md` each. `agents.md` exists only at the root. Bare names in documents (`backend.md` 4.2) resolve per the skill `find-rule`.
- The code skeleton is in `ai-docs/skeleton/` (not at `backend/`, `frontend/`); Epic 0 decides where it lands. Its seed `default_team_profile.sql` lists write paths such as ai-docs/architecture.md: those describe the team of a *generated* project, not this repository; leave them.
- The plan: `ai-docs/plan.md`, `ai-docs/goals/`, `ai-docs/epics/`, `ai-docs/stories/`, `ai-docs/tasks/`. Where the work stands: `todo.md` at the root, kept true after every task, story, decision and deployment; how to start or resume: `kickoff-prompt.md`.
- Screens: skill `screen-build`; BPMN: skill `bpmn-model`; both point into `ai-docs/ui-mockup/` and `ai-docs/bpmn/`.
- Decisions of 13 November 2026: everything on the local machine with Docker, dev, test, prod as compose projects (ADR-041); `workflow-engine` is its own Git repository inside `~/DEV`, which ignores it (`~/DEV/.gitignore`); toolchain = what is installed (Java 21.0.12, Node 24.14.1, npm 11, Gradle wrapper); runs ask the client only in the rare unfixable case and wait for tokens (`requirements.md` 0.4, FR-EXE-07, 08, 11).
- After moving any file run `node .claude/skills/docs-check/check-refs.mjs` (skill `docs-check`).

## Files protected on purpose

- `CLAUDE.md`, `agents.md` and the 13 `.claude/skills/<area>/<area>.md` documents are read-only on disk (mode 444), set by the Product Owner. Do not `chmod` them; propose the change. Only the human (or a session the human explicitly asks to restructure the documents) lifts it and restores 444 afterwards.
- `.claude/settings.json` and `.claude/hooks/` cannot be changed by any agent (global guard, AGT-081), not even by the main session. Shell commands that mention `.claude/hooks/` together with `cp`, `mv`, `>` and similar are blocked too.

## Guard behaviour worth knowing

- The global guard blocks a write that *adds* a test-disabling pattern, even in prose: the JUnit disable annotation written with its at-sign, a Vitest or Playwright skip or only call written as code, Gradle ignore-failures set to true. In documents, describe them in words.
- zsh: an argument that starts with `=` (for example a line of equals signs given to `echo`) is expanded as a command path and fails; use `#####` as a separator.

## Known gaps in the documents (raise, do not guess)

- `agents.md` AGT-042 names the owners of documents in "`workflow.md` 12.6", but `workflow.md` 12 has no such list. Owners used meanwhile: the write paths of the guard (ROLE_PATHS in .claude/hooks/guard.mjs)
- `i18n.md` has no owner role in the guard's path rules.
- `requirements.md` 0.3 does not yet cover every feature of `software-elements.md` 0.2 (`ai-docs/README.md` 6).
- `software-elements.md` 2 lists "6 Ready to run" for `workflows`; the screen number is 8 (6 is the AI team).
