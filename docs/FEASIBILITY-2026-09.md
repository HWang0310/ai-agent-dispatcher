# Feasibility Study — 2026-09

This document is the durable evidence summary for the feasibility phase of AI Agent Dispatcher. It intentionally summarizes engineering facts and decisions instead of reproducing chat transcripts.

## 1. Original problem

The Owner's existing development loop was already strong after an execution Agent reached GitHub:

```text
Agent implementation
-> commit / push / PR
-> GitHub evidence
-> ChatGPT PM review
-> PASS / NEEDS_CORRECTION / HOLD
```

The main waste was the Owner acting as a message relay before execution:

```text
ChatGPT PM prepares task
-> Owner copies long prompt
-> switches to desktop Agent
-> finds the right project/session
-> pastes task
-> starts execution
```

The project's goal is therefore not "build a general autonomous Agent platform." It is to remove mechanical transport and context-relay work while preserving PM decision authority and GitHub-based review.

## 2. Desired Owner experience

Target experience:

```text
Owner <-> ChatGPT PM
            |
            v
        durable dispatch
            |
      correct execution backend
            |
      implementation evidence
            |
          GitHub
            |
      ChatGPT PM review
```

The Owner should not need to repeatedly move long prompts, project paths, task context, acceptance criteria, or correction instructions between tools.

## 3. Codex local Agent surface probe

### Environment

Probe environment:

```text
macOS 26.6.2 (25G83)
arm64
```

Installed CLI:

```text
/Applications/ChatGPT.app/Contents/Resources/codex
codex-cli 0.153.4
```

### Verified surfaces

The probe verified that Codex can be driven as a machine execution surface through its CLI:

- `codex exec` noninteractive execution;
- prompt delivery through stdin;
- JSONL machine-readable output with `--json`;
- stable thread/session identity in output;
- workspace binding with `-C`;
- resume routing through `codex exec resume`.

A minimal read-only probe returned the exact requested result:

```text
DISPATCH_CODEX_POC_OK
```

The JSONL stream included events such as:

```text
thread.started
turn.started
agent_message
turn.completed
```

### Resume evidence

A non-ephemeral thread was created and a resume command resolved to the same thread identity.

The follow-up model turn encountered a service-capacity error for the selected model. Therefore:

```text
Session/thread routing: verified
Complete continuation turn: partial evidence only
```

The capacity failure is treated as a backend availability/retry condition, not evidence that task identity should be duplicated.

### Codex conclusion

```text
Codex automated dispatch: PASS
Codex session resume: PARTIAL / routing verified
```

A future deterministic local process can poll or receive GitHub task state without invoking a model. Model/token consumption begins only when a real task is dispatched to `codex exec`.

Conceptual path:

```text
GitHub Codex READY task
-> local lightweight dispatcher
-> selected Codex profile/model/reasoning configuration
-> codex exec
-> GitHub evidence
```

Exact model/profile names are not frozen into project memory because account availability and routing policy can change.

## 4. TeleAgent local Agent surface probe

### Installed application

Probe facts:

```text
Application: /Applications/TeleAgent.app
Bundle ID: com.teleai.superagent
Version: 2.4.1 / 2.4.1.1
```

### URL scheme

A registered `teleai-cowork` URL scheme was found.

Observed routes included capabilities conceptually corresponding to:

```text
open
auth
settings
conversation(sessionId)
```

The conversation route could navigate to an existing session. No supported deep link was verified for:

```text
submit arbitrary prompt
start arbitrary task
resume execution turn
```

Conclusion: the URL scheme alone did not provide an external dispatch contract.

## 5. TeleAgent protocol discovery

A second read-only probe examined locally shipped resources and renderer/client code without extracting or exposing secrets.

### Internal session service

The application contains an internal local service with session-oriented routes including patterns such as:

```text
GET /global/health
GET /session
POST /session
GET/POST /session/{sessionID}
GET /session/{sessionID}/message
POST /session/{sessionID}/message
POST /session/{sessionID}/prompt_async
GET /session/status
```

Internal authentication references included local session-key/HMAC, bearer, internal basic-auth, scheduler-token, and tools-bridge mechanisms.

No stable, documented external credential handoff was verified.

### Internal scheduler

The app bundle includes scheduler components, including a scheduler binary/daemon and task schema concepts such as:

```text
prompt
project_path
agent
provider/model
job_id
run_id
session_id
```

Internal scheduling capability demonstrates that the application itself can coordinate task execution, but **internal implementation is not equivalent to an officially supported external automation surface**.

The Owner's installed 2.4.1 client did not expose a confirmed user-facing Scheduled Task entry during the feasibility work.

### Public-product observation

During the 2026-09 research, public product messaging referenced autonomous task scheduling / scheduled tasks. However, that marketing/product description did not match a capability the Owner had actually confirmed in the installed client.

Therefore the architecture deliberately did **not** treat public wording or bundle internals as a supported contract. A future resume must re-check the then-current official product surface.

### TeleAgent protocol conclusion

```text
Internal automation capability exists: YES
Stable supported external trigger contract confirmed: NO
Production use of private auth/internal endpoints: REJECTED
```

## 6. Accessibility and UI automation probe

Accessibility inspection showed that the desktop UI can be partially observed, including a window, task-entry semantics, and action buttons.

However, the production requirements were not met:

- no stable prompt-field AX identifier;
- weak/stale native AX tree compared with renderer-level structure;
- session identity not reliably exposed through window metadata;
- renderer re-renders can change element identity/state;
- run/submit state was not robustly provable without brittle UI assumptions;
- coordinate-free end-to-end submission was not established.

No production task was submitted through this probe.

Conclusion:

```text
Accessibility production adapter: REJECTED
```

The project also rejects coordinate clicks, image recognition, keyboard macros, or clipboard-plus-click automation as merely different forms of the same brittle boundary.

## 7. Permanent-session polling was rejected

Real interactive TeleAgent sessions can stop, become busy, or require manual continuation.

Therefore a design such as:

```text
one TeleAgent conversation
-> while true
-> poll GitHub forever
```

is not accepted as a daemon architecture.

A model conversation is not a process supervisor.

## 8. Architecture alternatives studied

The feasibility phase considered multiple distinct patterns:

1. official TeleAgent scheduler / CLI / API / inbound trigger;
2. one fixed Manual Claim action that restores the full GitHub task;
3. a TeleAgent Skill used as the task-recovery entry;
4. Codex automatic dispatch plus TeleAgent semi-automatic claim;
5. GitHub Actions/App as control plane;
6. filesystem inbox / file watcher / command files;
7. macOS Shortcuts / Services / Apple Events;
8. MCP-based tool access;
9. generic Agent-to-Agent messaging.

The key conclusion was that many mechanisms can store or transport task data, but they cannot reliably **wake a stopped desktop Agent** without a supported receiving/trigger surface.

MCP, GitHub Actions, filesystem events, URL schemes, and local launch mechanisms are useful only if the final application boundary exposes an official entry point.

## 9. Independent architecture review

An independent GPT-6 architecture review was performed after the local probes.

It independently converged on the same main direction:

```text
GitHub = durable truth + control plane
Codex = local automatic execution
TeleAgent = desired primary execution backend
Trigger = replaceable adapter
```

Its highest-ranked current fallback was a fixed Manual Claim action that removes prompt copying and context movement while keeping one deliberate human trigger.

This agreement increased confidence in the architecture boundary, but model opinion is not treated as a substitute for product/API evidence.

## 10. Recommended architecture

The durable architecture is intentionally split into stable and replaceable layers.

### Stable layer

```text
ChatGPT PM
-> durable task/dispatch contract
-> GitHub control plane
-> claim/idempotency/execution state
-> GitHub evidence
-> PM review
```

### Replaceable trigger/execution layer

Conceptually:

```text
backend=codex
-> CodexLocalExecAdapter
-> codex exec

backend=teleagent
-> current fallback: ManualClaimAdapter
-> future: OfficialSchedulerAdapter / OfficialCLIAdapter / InboundEventAdapter
```

The names above describe architecture roles, not implemented classes.

## 11. Future task-state concepts

No task contract has been implemented yet, but feasibility work identified the likely durable identity/state separation:

```text
task_id             durable PM-level task identity
dispatch_id         one dispatch attempt / idempotency identity
backend_session_ref backend session/thread identity when applicable
backend_run_ref     backend run identity when applicable
workspace           canonical project path
branch              execution branch when applicable
```

Candidate state progression:

```text
READY -> CLAIMED -> RUNNING -> PM_REVIEW -> CLOSED
```

If polling is ever used, polling cadence must not equal task timeout. A future implementation should use claim/lease/idempotency semantics so later pollers do not duplicate a still-running task.

A valid running lease should prevent duplicate claims. An expired lease should trigger a recovery probe of GitHub/workspace/backend evidence before any re-execution.

These are feasibility design constraints, not an implemented canonical schema.

## 12. Rejected production approaches

The following were explicitly rejected during feasibility:

### REJECT — undocumented/private TeleAgent HTTP automation

Reasons:

- no stable external authentication handoff;
- tight coupling to internal implementation;
- upgrade fragility;
- unnecessary support/account/compliance uncertainty;
- poor long-term maintenance economics.

### REJECT — Accessibility/UI automation

Reasons:

- unstable selectors and renderer structure;
- session ambiguity;
- brittle run-state confirmation;
- recurring maintenance cost.

### REJECT — permanent TeleAgent polling session

Reasons:

- interactive sessions can stop;
- recovery is ambiguous;
- model conversation is not a daemon;
- duplicate/restart handling becomes unreliable.

### REJECT — credential extraction / Electron injection / patching

Reason: violates the intended stable product boundary and creates unnecessary risk.

### REJECT — "GitHub webhook alone" as a TeleAgent solution

GitHub can publish an event, but that does not solve the final local application trigger without an official receiver.

## 13. Known Manual Claim V0 fallback

A semi-automatic V0 remains technically useful if the Owner later wants it:

```text
ChatGPT PM
-> GitHub READY task

Owner
-> one fixed action such as "claim work"

TeleAgent
-> discover assigned READY task
-> claim
-> restore workspace/project/branch/prompt/acceptance
-> execute
-> commit/push/PR
-> update GitHub state
```

This eliminates long prompt copying and most context movement while retaining one explicit trigger.

It was considered the safest practical fallback under the observed constraints.

## 14. Current limitation

The project cannot currently claim a clean, verified, fully unattended TeleAgent-first path because no stable official automatic trigger has been confirmed in the installed desktop client.

This is the single most important limitation.

## 15. Owner decision

After feasibility completion, the Owner chose **not to implement Manual Claim V0 immediately**.

Reasoning:

- TeleAgent is the most important practical execution backend for this project;
- the Owner is not under time pressure to ship Dispatcher immediately;
- long-term stability and low maintenance are more important than eliminating the final manual trigger today;
- a brittle workaround would create a maintenance project larger than the mechanical work it removes.

Formal state:

```text
FEASIBILITY: COMPLETE
ARCHITECTURE FEASIBILITY: PASS
IMPLEMENTATION: HOLD
```

This is a paused project, not a failed project.

## 16. Resume triggers

Resume implementation planning if any of the following becomes true:

1. TeleAgent officially exposes stable Scheduled Tasks appropriate for local execution;
2. TeleAgent officially exposes a supported CLI;
3. TeleAgent officially exposes a supported authenticated API;
4. TeleAgent officially exposes a supported inbound event/webhook/trigger or equivalent extension surface;
5. the Owner explicitly chooses Manual Claim V0 or another path;
6. Codex execution surfaces change materially enough to affect the architecture.

On resume, re-test current product surfaces. Do not assume 2026-09 versions, marketing wording, model names, quotas, or internal endpoints remain current.
