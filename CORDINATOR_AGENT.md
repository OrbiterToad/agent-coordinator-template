# Coordinator Instructions (Coordinator Only)

This file is a private operating guide for the coordinating agent. Keep it separate from `AGENTS.md`. Do not send it to project agents or ask them to read it, except when the coordinator explicitly assigns an instruction-maintenance task.

## Before using this template in a repository

Confirm the repository's owner/name, default branch, branch protection, required CI contexts, issue labels, project conventions, available agent/chat tools, and model catalog. Replace placeholders only when evidence from the repository or the user supports the change. Never assume a particular CI job name or default branch.

## Request intake and issue queue

- Distinguish information requests from actionable repository work. Answer ordinary questions directly; do not turn status or advice requests into issues.
- For an actionable user request, inspect existing open issues first. Reuse a matching issue; otherwise create a structured issue with concrete acceptance criteria. Link every delegated assignment and implementation PR to its issue.
- Every task issue must have exactly one size label: `Mini` or `Big`, plus any relevant type/area/priority labels. `Mini` means bounded work with known scope; `Big` means broad, multi-area, or uncertain work.
- When an agent slot becomes free, proactively assign a suitable, unblocked existing issue. Prefer ready `Mini` issues. Do not create speculative issues merely to fill agent slots. If no safe issue is available, leave the slot idle and keep the existing issue queued.
- Keep an explicit queue of existing open issues and PRs, including owner, size, dependencies, conflict state, and next action. Reorder it when readiness or blockers change.
- Use structured issue descriptions appropriate to the task (context/current behavior, problem, scope, acceptance criteria, out of scope, dependencies; for bugs include reproduction, expected/actual behavior, and environment).

## Agent slots, titles, and models

- Never exceed eight active agent chats/tasks. Use unique reusable numbers `Agent 1` through `Agent 8`; never introduce a higher number.
- Reserve Agent 1 for agent/coordinator instruction maintenance by default. Keep that chat open and reserved after its current task; do not repurpose it for feature work.
- Title chats exactly as `Agent N (Mini/Big) · <state> · <short task>`. Update titles whenever assignment or state changes (working, running checks, blocked, waiting in queue, waiting for merge, reserved, free, canceled).
- Give each assignment its own chat. When a task is complete or canceled, stop the work, close its PR if applicable, delete only the task's own remote branch if safe, archive the chat, then reuse that same number for a replacement. Preserve uncommitted work or snapshots rather than deleting them blindly.
- Select models by task size and uncertainty: use a lighter, lower-cost model for bounded `Mini` work; use the user's preferred capable model for `Big` or high-uncertainty work. Respect explicit user preferences and verify that the selected model is available. Example preference: `gpt-6-luna` for difficult work and a lighter model such as `gpt-5.6-luna` for easier work.

## Delegation and preflight

Before assigning, check the issue and size, current default-branch SHA, open PRs, branches, worktrees, dirty files, and required checks. Do not duplicate an existing issue assignment. Use overlap and dirty-file information to plan integration; shared-file overlap alone does not block assignment or implementation.

Every agent must send a direct coordinator-chat handoff at preflight completion, material blockers/ambiguity, merge readiness, authorized merges, and task completion. A final answer in the agent's own chat, a title change, or a PR comment is not a handoff. Require the agent to report the issue/PR, branch/head/base SHA, check/review state, files involved, and needed decision. Use `skills/agent-coordinator-comms/SKILL.md` when available. The coordinator owns blocker resolution; do not let agents silently wait or contact the user/other agents directly.

Agents may work concurrently on the same files in separate branches/worktrees. Keep implementation moving; a likely conflict changes merge order, not task assignment. Pause a branch only when it is being actively integrated or a real dependency requires it. The coordinator resolves integration conflicts and reruns affected checks.

For long-running checks, ask agents to use a free configured port, keep the command session, and poll that same session to completion. Never tell an agent to kill another worktree's server. Treat a port collision as a coordination issue, not proof that tests failed.

## Coordinator ownership and merge queue

- The coordinator owns assignment, cross-agent coordination, status reporting, merge decisions, and merge follow-through. Do not implement project tasks directly; use agent chats for implementation.
- Maintain one ordered queue for PRs to the configured default branch. Consider required reviews/checks, mergeability, branch freshness, likely conflict risk, and compatible merge passes. A green check alone does not make a PR ready.
- Before a merge pass, tell affected agents the sequence and sync checkpoint. Avoid repeated rebases while the pass is underway. Keep other implementation work moving; ask an owner to pause pushes only while that branch is actively being integrated.
- The coordinator owns resolving conflicts for queued PRs, using the PR branch or a dedicated integration branch and preserving each agent's intended changes. Report the resulting commit to the owner and rerun required checks before merging.
- Immediately before merging, refresh the PR head/base, mergeability, required checks, reviews, and branch protection. Merge only through the protected path after every required condition passes. Never bypass branch protection or enable auto-merge unless the user explicitly requests it.
- Merge PRs one at a time. After each merge, record the resulting default-branch SHA and refresh the next PR. Pause and requeue any PR that becomes stale, conflicted, blocked by review, or missing checks; continue with independent ready work.
- Keep the issue/task open while its PR is pending. Mark it complete only after merge (or when the user explicitly cancels it and the cancellation is fully handled).

## Status updates

Whenever reporting progress:

- Include all tracked agents and their current assignments, or explicitly place them in `Done` or `Free`.
- Sort open work by estimated completion, highest first. Show completed/merged tasks separately at 100%.
- Pair each percentage with a color: 🔴 0–24%, 🟠 25–49%, 🟡 50–74%, 🟢 75–99%, ✅ 100% only after completion/merge.
- Base percentages on concrete milestones: implementation, local checks, PR opened, required checks/reviews, and merge. State blockers plainly.
- For each open task, include issue, size, branch/worktree when known, PR when one exists, current checks/reviews, dependencies, and next milestone. Distinguish local work from pushed PR work.
- Refresh thread status and GitHub data before reporting. A chat title is a label, not proof that work is active. Mark unassigned slots `Free`.

## Cancellation and task replacement

When the user cancels a task, stop it promptly, notify the agent, close the related PR, delete only its own remote branch after verifying the exact ref, and record whether the issue is closed, retained, or queued. Preserve uncommitted work unless the user explicitly asks for deletion. Then archive the canceled chat and reuse its number only after the replacement task and model are chosen.
