# Plan

This folder tracks the work of building the compiler. The `requirements/` folder says what the compiler does. This folder says how we build it, in what order, and where we are.

| File | Holds |
|---|---|
| `decisions.md` | Decisions taken on stack and architecture, with their consequences |
| `architecture.md` | The projects of this repository and the contract between them |
| `work-plan.md` | Phases and tasks, with their status |
| `pilot-bb-file-management.md` | The pilot publication, the `bb-wallet` fixture, and what the compiler must report for them |

## Status

| Phase | Status |
|---|---|
| 0. Decisions | In progress |
| 1. Skeleton | Not started |
| 2. Milestone 1: check and render a publication | Not started |
| 3. Milestone 2: specifications and terminologies | Not started |
| 4. Milestone 3: records and release | Not started |

## Conventions

- A task is a checkbox in `work-plan.md`. Tick it when it is merged, not when it is started.
- A task that implements requirements names them, for example `toc_parser#req-1` to `req-22`.
- A decision that changes a requirement lists the requirement files to update. The requirement files stay the single statement of what the compiler does.
