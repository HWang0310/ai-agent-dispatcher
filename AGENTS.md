# AGENTS.md

This file is the project recovery entry for AI Agent Dispatcher.

## 1. Recovery protocol for a new PM / Agent

Do not ask the Owner to retell project history if GitHub contains the answer.

Recover in this order:

1. Resolve `HWang0310/engineering-journal` current remote default branch and exact HEAD.
2. Read its latest `README.md`, `NEW-SESSION-BOOTSTRAP.md`, `ENGINEERING-STANDARDS.md`, and `RESTRICTED-CONTENT-STANDARD.md`.
3. Load any additional canonical standards triggered by the current task.
4. Read this repository's `README.md`.
5. Read `PROJECT-MEMORY.md`.
6. Read `CURRENT-STATE.md`.
7. Read `docs/FEASIBILITY-2026-09.md` when the evidence behind current architecture decisions is needed.
8. Inspect live GitHub Issues, PRs, branches, and current `main` HEAD.

Current remote GitHub facts take precedence over historical ChatGPT memory, old prompts, stale handoffs, or old backend assumptions.

## 2. Current project gate

Current status is **HOLD** after feasibility completion.

Do not resume implementation merely because a new session started.

Implementation may resume only when either:

- a resume trigger recorded in `CURRENT-STATE.md` becomes true and the PM revalidates it; or
- the Owner explicitly authorizes a new implementation path.

The known Manual Claim V0 is a fallback, not an active task.

## 3. Roles

Use generic project roles unless durable project facts later justify a named roster:

- **Owner:** product goals, priority, risk acceptance, irreversible business choices.
- **Project Manager:** technical solution, architecture, task split, backend routing, parallelism, verification depth, review, merge gate, project-state decisions.
- **Writer:** scoped implementation work.
- **Reviewer:** read-only or review-focused role when risk justifies it.

There is currently **no durable named engineer roster** for this project.

Backend identity is not engineer identity.

## 4. Dispatch discovery invariant

Before dispatching any Agent/backend, re-read the latest `engineering-journal`:

- `AGENT-OPERATING-MODEL.md`
- `BACKEND-CAPABILITY-CERTIFICATION.md`

Do not copy a historical backend ranking into this repository. Current routing policy belongs to the canonical standards repository.

## 5. Architecture boundary

The project architecture must preserve these responsibilities:

```text
ChatGPT PM           = decisions
GitHub               = durable truth + control plane
Dispatcher / Trigger = deterministic transport / execution bridge
Execution backends   = execution
```

The Dispatcher must not silently become an autonomous manager that decides task scope, model/backend selection, acceptance, merge, or product direction.

## 6. Current architecture facts

- GitHub is the intended durable dispatch/control plane.
- Codex has a verified machine-driving surface through its installed CLI.
- TeleAgent is the desired primary execution backend, but a stable official automatic external trigger is not currently confirmed for the installed client.
- The trigger adapter is intentionally replaceable.
- Manual Claim V0 is technically plausible but deliberately not implemented while the project is on HOLD.

For probe details and rejected approaches, use `docs/FEASIBILITY-2026-09.md` rather than reconstructing history from chat.

## 7. Rejected production approaches

Do not reintroduce the following without a material new fact or explicit Owner boundary change:

- reverse engineering undocumented/private TeleAgent authentication or prompt APIs;
- extracting or reusing private process credentials/tokens;
- Electron injection or patching;
- Accessibility, coordinates, image recognition, keyboard macro, or clipboard-driven production automation;
- treating a permanent TeleAgent conversation as a reliable daemon.

If an official product surface later replaces one of these with a documented supported contract, re-evaluate the official surface rather than the historical hack.

## 8. Task / Git rules

Follow latest `engineering-journal` for task depth, Task ID threshold, branch/PR mechanics, exact-SHA review, local-first vs SAFE_REMOTE_FIRST, restricted content, and merge acceptance.

High-risk architecture/Contract changes require PM review at the exact candidate SHA when applicable.

Agents do not self-approve the project. Final `PASS / HOLD / NEEDS_CORRECTION` belongs to the PM.

## 9. Local workspace

Canonical local workspace:

`/Users/hwang/Movies/Program/ai-agent-dispatcher/`

Do not create a second active clone merely for convenience. Protect any dirty/unpushed valid work before reconciling local state with remote.

## 10. Project memory discipline

`PROJECT-MEMORY.md` stores only durable truth that changes future engineering judgment.

Ordinary fixes, temporary backend choices, correction rounds, one-off logs, and transient review details belong in GitHub Issues/PRs/commits instead of being copied into Project Memory.
