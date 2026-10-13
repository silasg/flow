# Flow
Flow is a flexible, skill-based workflow for brainstorming, design, task planning, and implementation.

This fork is based on [Tobias Walle's original Flow](https://github.com/tobias-walle/flow). Many thanks to Tobias for creating the workflow and publishing it. The original version is intentionally small and unopinionated; install it from `tobias-walle/flow` if you prefer that approach.

This fork keeps Flow's modular structure while adding:

- an approval preview with building-block diagrams, key-file tables, call graphs, and a verification strategy before writing a design;
- implementation-ready tasks that reduce repeated discovery;
- bounded initial discovery for very large codebases; and
- an explicit choice of testing strategy, with guidance for bug fixes, features, refactoring, configuration, and documentation changes.

For me, the original sequence felt slightly unbalanced: requirements and intent—the **why**—were explored together during brainstorming, and implementation tasks—the **how**—were agreed together afterward, but the design—the **what**—was created as the only non-interactive step between them. This fork adds a design preview and approval step so the user and agent can see, discuss, and sharpen that design together. The design is too important to review only when the user happens to ask for more detail during brainstorming.

Skill-based workflows always involve a trade-off: you give up control but gain better agent performance by enforcing best practices.

The focus areas remain:
- **Modularity**: The skills must be easy to adapt into other workflows.
- **Readability**: Each skill should stay small and easy to understand. If you cannot understand the main skills in your workflow, you are losing control.
- **Customizability**: The skills stay focused and relatively unopinionated. Adapt them to your needs instead of blindly accepting their design choices.

## Installation
Install this fork with:

```sh
npx skills add silasg/flow
```

Alternatively, copy the [`skills/`](./skills/) directory into your project's or home directory's `.agents/skills` or `.claude/skills` folder.

To install Tobias Walle's original version instead:

```sh
npx skills add tobias-walle/flow
```

## Motivation
A lot of popular skills are extremely verbose and overengineered. This happens naturally if you rely too much on LLMs without guiding them.
I believe you must push against this trend to keep quality high. This workflow is my attempt to do this.

Enough talking, the main workflow is:
1. `/brainstorm`: Discuss a topic with your agent with the goal of reaching alignment (heavily inspired by Matt Pocock's [`/grill-me`](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) skill, but with a bit more guidance).
2. `/create-design`: Review and sharpen a concrete design preview together, then save the approved design for a later session. The format remains flexible, while important building blocks, files, runtime calls, verification choices, and decisions become explicit before task planning.
3. `/create-tasks`: Split work across multiple tasks that can be implemented in multiple sessions.
4. `/implement-tasks`: Implement the tasks created in the previous step, either in one or more sessions or by using subagents with or without worktrees. You could also build a Ralph loop around it. Your choice!

Two helper skills support this workflow. `/be-visual` adds diagrams. `/changes-folder` keeps each change's context together across phases:

```text
.agents/changes/
└── 2026-12-31-42-oauth-support/  # Issue reference is optional
    ├── design.md                  # Decisions and context
    ├── tasks/                     # Implementation tasks
    └── prototype/                 # Optional supporting artifacts
```

Other task-specific files and folders can be added freely.

Let's walk through an example:
1. `/brainstorm Let's brainstorm how we can add auth to my app. I already know I want to support OAuth2 and login via Google.`
2. *Q&A session around the main design decisions is happening*
3. `Now let's design the main interfaces and file structure` <- You are guiding the discussion into areas you want to align on
4. *Q&A session around architecture and code design*
5. `/create-design` <- Let's save our results. This will create a new file in `.agents/changes/YYYY-MM-DD-oauth-support/design.md`.
6. `/new` or `/clear` <- This is a good handoff point. Let's start a new session to keep the sessions focused. Of course you can decide to stay in the same session if you prefer or the task is small.
7. `/create-tasks for oauth-support` <- This creates one task file for each vertical slice in `.agents/changes/YYYY-MM-DD-oauth-support/tasks/`.
8. `/new` or `/clear` <- Another good handoff point.
9. `/implement-tasks Implement all tasks of oauth-support using subagents. Commit between each task.` <- You decide how you want to implement the tasks.

Basically, each phase is optional. Directly implement after `/brainstorm`? Your choice. You only want to use `/create-design` but not `/create-tasks` or `/implement-tasks`? Fine with me.

## Inspiration
As I follow the space quite closely, I have mostly combined existing ideas. The main inspirations were:
- [Matt Pocock Skills](https://github.com/mattpocock/skills): I really like Matt Pocock's minimal skill design. His skills are often quite readable and unopinionated. I have different opinions on some of the details of his skills, which is why I felt the need to create my own workflow.
- [Research, Plan, Implement from "No Vibes Allowed" by Dexter Horthy](https://www.youtube.com/watch?v=rmvDxxNubIg): I used a customized version of this workflow in the first half of the year, and it really helped me conceptualize some of the experiences I had. The main lessons around context engineering are still in this workflow. His latest workflow, [CRISPY](https://www.youtube.com/watch?v=YwZR6tc7qYg), also influenced the modular design of this workflow.
- [`/show-me` by Dexter Horthy](https://github.com/humanlayer/skills/blob/main/plugins/show-me/skills/show-me/SKILL.md): Our `/be-visual` skill is basically a lightweight version of this skill.
- [OpenSpec](https://openspec.dev/): Putting all relevant files into a single change folder was heavily inspired by OpenSpec.
