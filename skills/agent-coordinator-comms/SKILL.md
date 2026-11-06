---
name: agent-coordinator-comms
description: "Route agent progress, blockers, and merge handoffs directly to a coordinator chat in Codex. Use when coordinating work across separate agent chats; this standardizes messages over existing chat tools rather than creating a socket."
---

# Agent Coordinator Comms

Use this skill when a task is part of a multi-chat agent workflow and a coordinator chat owns task assignment, integration, or merges.

## Direct routing

- Treat a final answer in your own task chat, a title change, or a comment in a PR as a report to the user—not as a coordinator handoff.
- Find the coordinator chat with the Codex chat list tool. Use the returned thread ID and host ID with `send_message_to_thread`; do not guess or hard-code a thread ID.
- Send a direct message at meaningful transitions: preflight complete, material progress, blocker, merge-ready, authorized merge complete, or task complete/ready for reassignment. Avoid repetitive messages that add no new information.
- If direct messaging is unavailable, say so plainly and include the intended handoff in the task's final message. Never claim the coordinator was notified when no message was sent.

## Handoff content

- **Required message header:** use `[Agent <number>] [#<issue> <short task>] [<current state>]` for agent messages and `[Coordinator] [#<issue> <short task>] [<current state>]` for coordinator messages. On the very next line, begin the details with `Further info:`. Keep the issue/task and state concise and current, choosing the state from `assignment`, `preflight complete`, `progress`, `blocked`, `merge request`, `decision`, `changes requested`, `merged`, or `ready for new task`, exactly as `AGENTS.md` defines them. Example:

  ```text
  [Agent <number>] [#<issue> <short task>] [merge request]
  Further info: I want to merge PR #<number>. Head `<sha>`, base `<sha>`; checks: <results>; review: <state>; mergeability: <state>.
  ```

- Coordinator replies use the same two-line structure, with `[Coordinator]` in the first field. For example:

  ```text
  [Coordinator] [#<issue> <short task>] [progress]
  Further info: PR #<number> merged; current default-branch SHA is `<sha>`. Resume the queued work from that base.
  ```

- **Preflight:** issue/task link and scope, the fetched default-branch name and base SHA, branch/worktree, duplicate/overlap scan, and required checks.
- **Progress:** what changed, current branch and commit, checks run or underway, and next step. Keep this brief.
- **Blocker:** what is blocked, the concrete cause, what you already tried, and the decision or action needed. Shared files alone are not a blocker when agents use isolated branches.
- **Merge-ready:** in the `Further info:` line, include the exact phrase “I want to merge PR #<number>.” Also include the PR link, tested head SHA, current base SHA, each required check's state, review/request state, mergeability/freshness, and any known conflict. If no PR exists because access failed, include the pushed branch, exact SHAs, local check results, and the access error so the coordinator can open it.
- **After merge:** only report “I have merged PR #<number>” in the `Further info:` line after the coordinator authorized the merge and the hosting service confirms it; include the resulting default-branch SHA.
- **Complete:** say the issue is complete and the agent is ready for reassignment. An open PR is not merged; keep it visible to the coordinator until resolved.

## Coordinator workflow

- Acknowledge direct handoffs and track their issue, branch, PR, head/base revisions, checks, and next action.
- Keep implementation moving across separate branches, including overlapping files. Queue conflicts for integration; do not tell agents to wait solely because another branch edits the same file.
- Before merging, refresh the PR, required checks, reviews, freshness, and mergeability. Get integration conflicts resolved by delegating them to an agent in the PR branch or a dedicated integration branch, preserving every side's intended change; rerun required checks, merge only through the repository's protected path, then send the resulting default-branch SHA to affected agents.
- In status reports, pair each percentage with its color marker and a ten-cell bar, as `CORDINATOR_AGENT.md` specifies (for example `🟢 ████████░░ 80%`), and keep agent chat titles updated on every state change.
- Use `wait_threads` for compact progress snapshots; use `read_thread` when a handoff needs detail. Do not treat chat titles or summaries as authoritative status.

This skill uses the Codex chat-messaging tools as the communication channel. It does not create a persistent socket, daemon, or background monitor; those require a separate transport service and deployment.
