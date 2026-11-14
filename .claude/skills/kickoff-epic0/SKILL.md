---
name: kickoff-epic0
description: "Kickoff of Phase 0 (Epic 0, technical setup) for the developer who takes it over: check the machine and the repository, explain the target for 20 November and the order of the stories, record who works on Epic 0, then start US-00.01. Use when a developer types /kickoff-epic0, once, before the first Epic 0 story; afterwards /next."
disable-model-invocation: true
---

# Kickoff of Phase 0 (Epic 0)

The plan is already approved by the Product Owner (the repository owner): do not plan Epic 0 again and do not reopen decisions D-01 to D-14 in `todo.md`. The documents win over this skill (AGT-080).

## 1. Check the machine and the repository (report each command and result)

- `git switch dev && git pull --rebase origin dev`; `git status` clean. All work happens on branches from `dev` with pull requests into `dev`; `main` is not for commits and pushes (CLAUDE.md 7, `kickoff-prompt.md` branch rules).
- `node .claude/hooks/selftest.mjs` (all pass) and `node .claude/skills/docs-check/check-refs.mjs` (0 broken).
- `java -version` (21), `node -v` (24), `npm -v`, `docker info` (running), `gh auth status` (signed in, collaborator of `ekarimova/workflow-engine`).
- `test -f .env && echo present` — only whether it exists; never read, print or stage it. Missing: tell the developer to create it with their own `ANTHROPIC_API_KEY` (needed for the live AI check of US-00.15, not before).
Anything missing: say what to install or do, and stop.

## 2. Explain Phase 0 in ten lines

Read `todo.md`, `ai-docs/plan.md` (section "Target for 20 November"), `ai-docs/epics/E-00.md` and `ai-docs/goals/G-01.md`. Tell the developer:
- what Epic 0 delivers by 20 November (setup up to local dev, with the AI check US-00.15) and that the business stories (wish to reviewed workflow) start right after US-00.10, possibly by other developers;
- the order of the stories and which are after 20 November;
- the open decisions in `todo.md` that belong to the owner (they are not this developer's to take);
- the loop of every story: `/next` plans it (the developer approves), builds it task by task with the role subagents, runs `ci/run all-local` and `/team-verification`, pushes the story branch and opens the pull request; the owner or another developer reviews and merges (never the author); `/next` then syncs `main` and deploys to dev.

## 3. Record the takeover

On a branch `docs-<yyyy-mm-dd>-epic0-kickoff` from `dev`: in `todo.md` write the developer's name (as they give it; no e-mail or other personal data) as Epic 0 developer under **In progress**, set E-00 and G-01 to Active (in `todo.md`, `ai-docs/epics/E-00.md`, `ai-docs/goals/G-01.md`, `ai-docs/plan.md`) and add a dated line under **Done**. Commit, push, open the pull request into `dev` (or include it in the first story's pull request if the developer prefers).

## 4. Start

Continue exactly as `/next US-00.01` (skill `next`): `/start-story US-00.01`. The human steps of T-00.01.2 that need the owner's GitHub rights (branch protection, collaborators) are listed for the owner; the developer does the rest.
