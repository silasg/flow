---
name: create-design
description: Create a design document from a brainstorming session or another discussion. Use when important decisions must persist in a visual document for humans.
---

Create a concise `design.md` in the change folder. Follow the `changes-folder` skill for folder conventions.

Before writing, present a short design preview and wait for the user's agreement. Only update an existing design if explicitly mentioned. Do not search for matching designs proactively.

Start the document with lightweight YAML frontmatter:

```yaml
---
datetime: <Current ISO 8601 date and time>
author: <Author name>
tags: [<tag>, <tag>]
---
```

Cover all important decisions from the source discussion. Include the context needed to understand each decision.

Clearly reference related documents and websites. Use paths or links that the reader can follow.

## Design preview and approval

Show the preview in this order. Omit sections that do not apply, and keep it short enough to scan.

1. **Building blocks:** Use focused diagrams to show the important components, boundaries, and relationships. Put the relevant file name in each block and mark it `[NEW]`, `[CHANGE]`, `[REMOVE]`, `[REPLACE]`, or `[EXISTING]`.

   ```text
   ┌────────────────────── PAGE ──────────────────────┐
   │ [CHANGE] ProjectsPage.tsx                        │
   └────────────────────────┬─────────────────────────┘
                            │ opens
                            ▼
   ┌────────────────────── DIALOG ────────────────────┐
   │ [NEW] CreateProjectDialog.tsx                    │
   └────────────────────────┬─────────────────────────┘
                            │ submits
                            ▼
   ┌────────────────────── SERVICE ───────────────────┐
   │ [CHANGE] project-service.ts                      │
   └────────────────────────┬─────────────────────────┘
                            │ POST /projects
                            ▼
   ┌──────────────────── CONTROLLER ──────────────────┐
   │ [CHANGE] projects-controller.ts                  │
   └──────────────────────────────────────────────────┘
   ```

2. **Key file changes:** List the important files in a table with columns `Change`, `File`, and `Planned change`. Include files that define the design, not every routine test, generated file, or configuration file.

   | Change | File | Planned change |
   |---|---|---|
   | **NEW** | `src/projects/CreateProjectDialog.tsx` | Collect and submit project details |
   | **CHANGE** | `src/projects/project-service.ts` | Add the project creation request |
   | **CHANGE** | `server/projects/projects-controller.ts` | Validate and handle project creation |
   | **EXISTING** | `server/projects/project-repository.ts` | Reuse the existing persistence boundary |

3. **Key call graphs:** Show the happy-path runtime calls for each important use case. Include only failure branches that affect a public contract, security, concurrency, transactions, or core business rules.

   ```text
   Use case: Create project

   User submits “Create project”
   └─ CreateProjectDialog.submit()
      └─ projectService.create(input)
         └─ POST /projects
            └─ ProjectsController.create(request)
               ├─ Project.create(request)
               └─ ProjectRepository.save(project)

   Important failure branches
   ├─ Invalid input ─────────────► 400 Bad Request
   └─ Project name already used ─► 409 Conflict
   ```

4. **Verification strategy:** State how the change should be verified and identify the most useful test seam. Prefer the lowest test level that proves the behavior without duplicating the same assertion across layers.

The examples are fictional and demonstrate presentation, not a required framework or architecture. Adapt their blocks, paths, and vocabulary to the target project.

Use real names and paths established during the discussion. Mark uncertain details instead of inventing them. Diagrams explain architecture, file tables make the scope concrete, and call graphs explain runtime behavior; avoid repeating the same information as prose.

Stop after the preview. Write or update `design.md` only after the user approves it.

## Full design

Place the approved preview at the beginning of `design.md`, followed by the detailed design. Use a free structure that fits the topic. Use concise language, a clear structure, and focused visualizations. Use the `be-visual` skill if available.

Use this fixed section for each key decision:

```md
## Key Decisions

### <Decision title>

- **Decision:** <Selected option>
- **Reason:** <Why this option was selected>
- **Trade-offs:** <Accepted disadvantages or rejected options>
```

Do not force other content into a fixed structure.

A design document is not necessarily an architecture decision record (ADR). If the change establishes a durable, cross-cutting architectural rule, check the project's existing ADRs and create or update one according to its conventions. Reference it from `design.md` instead of duplicating it.
