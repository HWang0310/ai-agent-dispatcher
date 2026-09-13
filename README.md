# AI Agent Dispatcher

Durable dispatch control plane and execution bridge for AI-assisted software development.

## Current status

- **Stage:** Feasibility complete
- **Status:** HOLD
- **Implementation:** Not started
- **Current blocker:** No confirmed stable, official automatic TeleAgent trigger in the installed desktop client
- **Canonical local workspace:** `/Users/hwang/Movies/Program/ai-agent-dispatcher/`
- **Durable source of truth:** this GitHub repository

This project is intentionally paused after feasibility work. `HOLD` does not mean the project failed; it means the architecture is understood, but implementation is deferred until the trigger boundary is good enough or the Owner explicitly chooses a semi-automatic fallback.

## Mission

The project exists to remove mechanical relay work from an AI-assisted software-development workflow. The target experience is that the Owner communicates with the ChatGPT Project Manager, the PM makes routing and engineering decisions, execution backends receive deterministic dispatches, GitHub carries durable state and evidence, and the PM reviews the resulting remote facts.

The system is **not** intended to become another autonomous project manager.

## Responsibility boundary

```text
Owner                = product goals, priority, risk acceptance, irreversible business choices
ChatGPT PM           = technical plan, task split, backend routing, parallelism, review, merge gate
GitHub               = durable truth + dispatch/control plane
Dispatcher / Trigger = deterministic transport and execution bridge
Execution backend    = implementation / test / commit / push / PR work
```

## Architecture direction

```text
                         GitHub
                 Dispatch / Control Plane
                    /                \
                   /                  \
          TeleAgent lane          Codex lane
          trigger adapter      local dispatcher
                   |                  |
              TeleAgent           codex exec
                   \                  /
                    \                /
                         GitHub
                           |
                    ChatGPT PM Review
```

The trigger layer is intentionally replaceable. A future official TeleAgent scheduler, CLI, inbound event, or authenticated API should be able to replace the trigger adapter without redesigning the durable task contract.

## Feasibility conclusions

- **Codex automated CLI dispatch:** PASS
- **Codex session resume:** PARTIAL; thread routing was verified, while a full continuation turn was blocked by service capacity during the probe
- **TeleAgent as an execution backend:** PASS
- **TeleAgent official automatic external trigger:** NOT CURRENTLY CONFIRMED
- **TeleAgent-first:** GO_WITH_LIMITATIONS
- **Manual Claim V0:** known feasible fallback, but not authorized for implementation at this time

Production approaches already rejected include undocumented/private TeleAgent API automation, Accessibility/coordinate automation, credential extraction, Electron injection, and treating a permanent model conversation as a daemon.

See [`docs/FEASIBILITY-2026-09.md`](docs/FEASIBILITY-2026-09.md) for the evidence summary.

## Recovery entry

A new PM or Agent must not depend on historical ChatGPT conversation memory. Recover in this order:

1. Read the **current remote default branch and exact HEAD** of [`HWang0310/engineering-journal`](https://github.com/HWang0310/engineering-journal), then follow its latest `NEW-SESSION-BOOTSTRAP.md` and applicable standards.
2. Read [`AGENTS.md`](AGENTS.md).
3. Read [`PROJECT-MEMORY.md`](PROJECT-MEMORY.md).
4. Read [`CURRENT-STATE.md`](CURRENT-STATE.md).
5. Read [`docs/FEASIBILITY-2026-09.md`](docs/FEASIBILITY-2026-09.md) only as needed for the evidence behind current decisions.
6. Inspect live GitHub Issues, PRs, branches, and current `main` HEAD.

**Current GitHub facts override stale chat memory, old handoffs, or old model assumptions.**

## Resume triggers

Re-open implementation planning when at least one of the following becomes true:

- TeleAgent officially exposes stable Scheduled Tasks suitable for local execution;
- TeleAgent officially exposes a supported CLI;
- TeleAgent officially exposes a supported authenticated API;
- TeleAgent officially exposes an inbound event/webhook/trigger or equivalent supported extension surface;
- the Owner explicitly authorizes the semi-automatic Manual Claim V0;
- a material change in Codex execution surfaces requires architecture re-evaluation.

Until then, do not start Dispatcher implementation merely because a new session has begun.
