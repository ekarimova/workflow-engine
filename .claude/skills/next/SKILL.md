---
name: next
description: "Continue the work on the workflow engine where it stopped: read todo.md and the checkpoint of the current story, find the next step (start a story, build its next task, verify, open the pull request, sync after the merge, deploy to dev) and do it. Use when a developer types /next, at the start of every session and after every merge."
argument-hint: "[US-ee.ss to pick a specific story]"
disable-model-invocation: true
---

# Next step

The documents win over this skill (AGT-080). Work on one story at a time (CLAUDE.md 3). Ask the human only for a decision that is theirs (PO, Architect) or a permission prompt; otherwise continue.

## 1. Find where the work stands

1. Read `todo.md` (Current position, Decisions needed, In progress, Next) and `ai-docs/plan.md` section "Target for 20 November". If Epic 0 has not started yet (E-00 Planned, no Epic 0 developer under In progress), stop and tell the developer to type `/kickoff-epic0` first.
2. Run `git status`, `git branch --show-current`, `git fetch origin` and `git log --oneline -5`. If the working tree has changes you did not make in this session, show them and ask before touching them.
3. Pick the story: the argument if given; else the story **In progress** in `todo.md` that names this developer, or has no name; else the first story in **Next** whose dependencies are done and that nobody has taken. With several developers, write your name (or "developer N") and the story into **In progress** before you start, so nobody else takes it.
4. Read its card `ai-docs/stories/<id>.md` and its **Checkpoint**: resume from the checkpoint, not from memory (AGT-067).

## 2. Do the next step of that story

| State of the story | Next step |
|---|---|
| Draft, no plan | `/start-story <id>` (Definition of Ready, task specs, the human approves the plan) |
| Ready or In progress | branch `us-ee.ss-short-title` from an up-to-date `main` if it does not exist; start the subagent of the next open task with its task spec (the main session starts subagents, WFL-050); after each task: commit on the story branch (subject + story id + task id), update the checkpoint and `todo.md` |
| All tasks done | `ci/run all-local` (steps that do not exist yet: "not run" with the reason), then `/team-verification <id>` |
| Verified | push the story branch (`git push -u origin <branch>`, never `main`) and `gh pr create` with the template; tell the human the pull-request link to review and merge on GitHub |
| Merged on GitHub | `git switch main`, `git pull --rebase origin main`, delete the local story branch; `ci/run deploy dev`, `ci/run verify dev` (when US-00.10 exists, else "not available"); story Done in the card, the epic file and `todo.md`; then go back to 1 for the next story |

Story-free work (a plan or document change): branch `docs-<yyyy-mm-dd>-<topic>`, commit, push, pull request, as above.

## 3. Before you stop (or when tokens run out)

Commit the work in progress on the story branch, write the checkpoint (last verified commit and run, next step, open questions) and update `todo.md`. The next `/next`, by you or anyone else, continues from there.

## Git rules

Commit and push only on story or docs branches. Never push to `main`, never force-push, never merge your own pull request (CLAUDE.md 3, guard hook). `.env` is never read, staged or committed.
