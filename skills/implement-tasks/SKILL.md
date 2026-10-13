---
name: implement-tasks
description: Implement the given .agents/changes/<change>/tasks. Only use if a concrete .agents/changes path or task is specified. Do not use for generic implementation tasks.
---

Implement a change or focused task. Follow the `changes-folder` skill for folder conventions.

1. Determine the scope:
   * If the user gave you a full change, the scope is all tasks of this change
   * If the user referenced a single task, the scope is only this task

2. Decide how to work:
   * Default: **In Context Implementation**
   * Explicit User Request: **Sequential Subagents**
   * Explicit User Request: **Parallel Subagents**

   The user can always override that recommendation.

## Implementing a task

1. Understand the task and its scope.
2. Read the referenced documents and key files.
   * For a very large codebase, keep initial discovery bounded. Start with the task's references, named symbols, direct callers, and owning tests. Prefer focused searches over broad scans.
   * Aim to reach the first concrete edit or test run after roughly 10–15 focused read/search calls. This is a heuristic, not a hard cap. If more discovery is necessary, state the specific uncertainty or risk that justifies it.
   * For a small or unfamiliar codebase, read enough surrounding context to avoid a locally plausible but globally inconsistent change.
3. Agree on the testing approach before implementation if the user has not already specified it. Recommend one of these options and briefly explain the trade-off:
   * **Test-first (TDD):** Default recommendation for bug fixes and behavior-heavy features. A bug fix starts with a failing regression test that reproduces the bug.
   * **Integration-level test-first:** Often the best feature trade-off when a stable public seam can prove the behavior with fewer brittle tests. It provides regression protection and executable documentation while reducing implementation time and token use.
   * **Tests after implementation:** Reasonable for low-risk plumbing or when the useful test seam only becomes clear during implementation. Add focused behavior coverage before considering the task complete.
   * **No new automated test:** Reasonable for documentation, comments, formatting, static configuration, generated artifacts, or mechanical metadata changes when existing validation commands prove correctness. Do not add tests that only restate a tool or schema check.
   * For refactoring, first confirm that existing tests characterize the behavior; add characterization tests when they do not.
   * Prefer the lowest stable seam that proves the behavior. Avoid duplicate assertions at unit, integration, and end-to-end levels unless each level protects a distinct risk.
4. Mark the next work items with `[-]`.
5. Follow the agreed testing approach. Batch related edits when useful, but keep behavior changes observable and independently verifiable.
6. Mark completed work items with `[x]`.
7. Run relevant checks and tests and fix issues directly.
8. Mark verification items already covered by the tests with `[x]`.
9. For remaining items:
   * If you can test them yourself with ad hoc scripts and experiments, do that. Mark the verification with `[-]`.
   * Fix mistakes directly. Use TDD for hard-to-detect logic bugs.
   * Mark with `[x]` once passed.
   * Add a note to each passed item that states how you verified it. For example: `**Note:** Verified via <reference to automated test>`.
   * Mark remaining items that you cannot test with `[ ] (manual testing required)`.
10. If the implementation requires a meaningful deviation from the design, do not silently change it. Adapt implementation details to the current codebase where necessary, but make design deviations and their reasons explicit.
11. Double-check the changes for completeness and documented best practices. Use a subagent only when the user requested subagents. Integrate relevant feedback directly.
12. Present a visual, structured, concise summary to the user. Include what was done, what problems occurred, how you resolved them, and what manual tests remain.

## Sequential Subagents

Use this mode only when the user explicitly requests subagents.

1. Read the change and task documents. Do not read source files unless they are relevant to the orchestration task.
2. Select the next task whose dependencies are complete.
3. Start one subagent for the task. Start the prompt with: `You are a subagent for task <filepath> of change <changepath>. Only focus on this task and respond with a clear summary of your work for the main agent`. Include the exact steps from `Implementing a task` and other relevant file references.
4. Start only one subagent at a time.
5. After all tasks are complete, use a subagent to check that the change is fully integrated.
6. Present a visual, structured, concise summary to the user. Include deviations from the design and their reasons.

## Parallel Subagents

Use this mode only when the user explicitly requests parallel subagents.

Follow `Sequential Subagents`, but use worktrees to isolate independent tasks.

Process only tasks whose dependencies are complete.

Merge completed worktrees into the current branch one at a time. Resolve integration issues directly.

If not otherwise specified in the project setup, store worktrees in `.agents/worktrees` and make sure it is in `.gitignore`
