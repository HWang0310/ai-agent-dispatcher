# PROJECT-MEMORY.md

This file contains durable project truth only.

## Project identity

- **Name:** AI Agent Dispatcher
- **Repository:** `HWang0310/ai-agent-dispatcher`
- **Canonical local workspace:** `/Users/hwang/Movies/Program/ai-agent-dispatcher/`
- **Project type:** independent software project
- **Current stage:** feasibility complete
- **Current status:** HOLD

## Mission

Reduce Owner mechanical relay work in an AI-assisted software-development workflow while preserving clear decision authority, recoverability, review quality, and backend flexibility.

The project is successful when the Owner can primarily communicate with the ChatGPT Project Manager while deterministic infrastructure delivers already-decided tasks to execution backends and GitHub preserves durable state and evidence.

## Durable responsibility boundary

### Owner

Owns:

- product goals;
- priority;
- risk acceptance;
- irreversible business choices.

### ChatGPT Project Manager

Owns:

- technical solution;
- architecture;
- task decomposition;
- Agent/backend routing;
- serial/parallel choice;
- validation depth;
- review;
- `PASS / NEEDS_CORRECTION / HOLD`;
- merge gate.

### GitHub

Acts as:

- durable engineering truth;
- project recovery source;
- intended dispatch/control plane;
- durable execution/review evidence store.

### Dispatcher / Trigger

Must remain a deterministic transport/execution bridge. It must not silently assume PM decision authority.

### Execution backends

Perform scoped execution such as implementation, tests, local work, commit, push, and PR creation.

## Durable architecture decisions

### ADR-001 — GitHub is the control-plane truth

**Decision:** use GitHub as the durable project truth and intended task/control plane rather than relying on ChatGPT conversation state.

**Why:** remote facts survive session replacement, model replacement, local process failure, and human context loss. A new PM must be able to recover the project from GitHub without the Owner replaying history.

### ADR-002 — Separate decision from transport

**Decision:** PM makes engineering decisions; Dispatcher transports already-decided work.

**Why:** this keeps routing, scope, review, acceptance, and merge authority in one accountable PM layer and prevents a local daemon from becoming an accidental autonomous manager.

### ADR-003 — Trigger is an adapter boundary

**Decision:** TeleAgent/Codex trigger mechanics must sit behind replaceable adapters rather than being baked into the durable task model.

**Why:** execution surfaces can change while project/task state should remain stable.

Expected conceptual adapters include:

- Codex local CLI execution adapter;
- TeleAgent manual-claim adapter as a fallback;
- future TeleAgent official scheduler / CLI / inbound trigger / authenticated API adapter.

These are architecture roles, not proof that all implementations currently exist.

### ADR-004 — Codex machine dispatch is feasible

**Decision:** treat the installed Codex CLI as a viable deterministic execution surface for future automated dispatch.

**Evidence:** noninteractive execution, stdin prompt delivery, JSONL output, thread identity, workspace binding, and resume routing were locally probed successfully; a complete continuation turn was not fully proven because the selected model hit service capacity during the probe.

### ADR-005 — TeleAgent remains the desired primary backend

**Decision:** preserve a TeleAgent-first product direction because it is the Owner's main practical execution backend.

**Constraint:** do not equate "works well as an interactive Agent" with "has a supported external automatic trigger." The latter is not currently confirmed for the installed desktop client.

### ADR-006 — Do not productionize private/internal TeleAgent surfaces

**Decision:** reject a production adapter based on undocumented local HTTP internals, private authentication material, credential extraction, Electron injection, or patching.

**Why:** brittle version coupling, unclear support boundary, unnecessary account/compliance risk, and poor long-term maintainability.

### ADR-007 — Do not productionize UI automation

**Decision:** reject Accessibility/coordinate/image-recognition/keyboard-macro/clipboard automation as the primary production trigger.

**Why:** unstable element identity, renderer changes, session ambiguity, weak execution-state confirmation, and ongoing maintenance burden.

### ADR-008 — Do not use a permanent model conversation as a daemon

**Decision:** reject a never-ending TeleAgent conversation that polls GitHub.

**Why:** real interactive sessions may stop or require manual continuation and therefore are not a reliable process supervisor.

### ADR-009 — Manual Claim V0 is a fallback, not the active plan

**Decision:** a semi-automatic flow in which the Owner performs one fixed "claim work" action and TeleAgent then restores task/project/prompt from GitHub is considered feasible enough to retain as a fallback.

**Owner decision:** do not implement it now. The project is paused while waiting for a better official automatic trigger unless the Owner later explicitly chooses this fallback.

### ADR-010 — Current project state is HOLD after feasibility

**Decision:** implementation is intentionally not started.

**Reason:** the architecture is understood, Codex automation is feasible, and TeleAgent execution is useful, but the most valuable TeleAgent-first zero-relay path lacks a confirmed stable official automatic trigger in the installed client.

`HOLD` means paused after feasibility, not project failure.

## Rejected directions unless facts materially change

- undocumented/private TeleAgent API automation;
- private credential/token extraction or reuse;
- Electron injection/patching;
- Accessibility or coordinate-driven production automation;
- image-recognition/keyboard-macro/clipboard-based production control;
- permanent TeleAgent polling conversation;
- assuming GitHub Actions, MCP, filesystem events, or a URL scheme can wake TeleAgent without a supported receiving/trigger contract.

## Resume conditions

Resume architecture/implementation when at least one durable new fact appears:

1. TeleAgent officially exposes stable Scheduled Tasks appropriate for local execution;
2. TeleAgent officially exposes a supported CLI;
3. TeleAgent officially exposes a supported authenticated API;
4. TeleAgent officially exposes a supported inbound event/webhook/trigger or equivalent extension surface;
5. the Owner explicitly authorizes Manual Claim V0 or another implementation path;
6. Codex execution surfaces materially change and require architecture re-evaluation.

Any resume must re-check current product capabilities rather than trusting the 2026-09 probe forever.

## Roster

There is currently no durable named engineer roster. Use generic PM / Writer / Reviewer roles unless future project facts justify a persistent roster.

Backend selection is operational routing and must be recovered from the latest canonical engineering standards, not frozen into this Project Memory.
