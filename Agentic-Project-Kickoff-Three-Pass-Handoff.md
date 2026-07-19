# Agentic Project Kickoff Skill — Three-Pass Finalization Handoff

This handoff is organized into three bounded Codex passes:

1. Structural cleanup
2. Workflow reconciliation
3. Validation and testing

Use the passes in order. Review Codex’s report after each pass before continuing.

---

# Pass 1 — Structural Cleanup

## Task

Update the existing local Codex skill package:

`agentic-project-kickoff`

This pass is limited to file structure, cleanup, metadata, and permissions.

Do not substantially rewrite the kickoff workflow. Do not run a forward test. Do not install the skill into `~/.agents/skills`. Do not create or modify a symlink.

Before making changes:

1. Inspect the complete skill folder.
2. Record the existing file tree.
3. Read `SKILL.md` sufficiently to distinguish current files from legacy files.
4. Preserve approved content unless these instructions explicitly delete, move, or rename it.

Do not delete or modify `.DS_Store` files during these passes unless the human
separately authorizes that cleanup. Exclude them from the logical skill tree and
structural assessment because they are macOS Finder metadata, not skill content.

## 1. Normalize permissions

Set:

- directories to `755`
- ordinary files, including Markdown and YAML, to `644`
- genuine executable scripts to `755` only when direct execution is required

Use commands equivalent to:

```bash
find /path/to/agentic-project-kickoff -type d -exec chmod 755 {} \;
find /path/to/agentic-project-kickoff -type f ! -name '.DS_Store' -exec chmod 644 {} \;
```

Restore `755` only to genuine executable scripts afterward. Do not change
ownership. Leave `.DS_Store` permissions unchanged.

## 2. Keep only the root README

Keep:

`agentic-project-kickoff/README.md`

The root README is a deliberate exception to generic minimal-skill packaging
guidance because this folder is also the human-managed, GitHub-facing source
that may be symlinked into the skills directory.

Delete every other `README.md` in the skill package, including:

`assets/templates/README.md`

Also delete:

`assets/templates/START_HERE_FOR_CODEX.md`

Do not substantially rewrite the root README in this pass.

## 3. Delete the Template Usage Guide completely

Delete any Template Usage Guide file, including:

- `assets/templates/TEMPLATE_USAGE_GUIDE.md`
- `Context/TEMPLATE_USAGE_GUIDE.md`

Search for and remove references to:

- `TEMPLATE_USAGE_GUIDE`
- `Template Usage Guide`
- `Template Guide`

Do not replace it with another guide. The root `README.md` will be the human-facing guide; `SKILL.md` remains authoritative for agent behavior.

## 4. Establish one authoritative Builder Playbook reference

Move or rename:

`assets/templates/BUILDER_PLAYBOOK.template.md`

to:

`references/BUILDER_PLAYBOOK.md`

Remove the old `.template.md` file.

There must be exactly one authoritative skill-level Builder Playbook:

`references/BUILDER_PLAYBOOK.md`

Use `references/` because the master playbook is guidance that Codex reads and
applies when needed. It is not an output asset and is not copied into projects
by default.

Do not create a project-local Builder Playbook in this pass.

Update obvious references so they point to the exact master path. Remove references to the old template path and ambiguous references to a nonexistent root-level or unspecified global playbook.

## 5. Delete the Project Learnings template completely

Delete:

`assets/templates/Context/Project_Learnings.template.md`

Also delete any duplicate or relocated Project Learnings template.

Search for:

- `Project_Learnings`
- `Project Learnings`
- `Context/Project_Learnings.md`
- `Project_Learnings.template.md`

Remove instructions to create, update, or include a standalone Project Learnings file. Do not replace it with another standalone learning-log file.

## 6. Add `agents/openai.yaml`

Create:

`agents/openai.yaml`

Use exactly:

```yaml
interface:
  display_name: "Agentic Project Kickoff"
  short_description: "Structure projects before implementation"
  default_prompt: "Use $agentic-project-kickoff to structure this project idea."
```

Do not add:

```yaml
policy:
  allow_implicit_invocation: false
```

Invocation behavior will be governed by `SKILL.md`.

## Expected structure after Pass 1

```text
agentic-project-kickoff/
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
├── references/
│   └── BUILDER_PLAYBOOK.md
└── assets/
    └── templates/
        ├── .gitignore.template
        ├── AGENTS.template.md
        ├── Context/
        │   ├── IDEA_INTAKE.template.md
        │   ├── Decision_Notes.template.md
        │   └── 3-Hop-Method.md
        ├── ProjectPlans/
        │   ├── STRATEGY.template.md
        │   ├── ROADMAP.template.md
        │   ├── DECISIONS.template.md
        │   └── TASKS.template.md
        └── docs/
            ├── phase0/
            │   ├── PHASE0_CHECKLIST.template.md
            │   └── READINESS_ASSESSMENT.template.md
            └── templates/
                ├── PHASE_PLAN.template.md
                ├── PHASE_CHECKLIST.template.md
                ├── REVIEW_SUMMARY.template.md
                ├── PHASE_RETROSPECTIVE.template.md
                └── PROJECT_RETROSPECTIVE.template.md
```

Preserve any additional approved file that clearly belongs in the package. Do not delete an uncertain file without reporting it.

## Pass 1 validation

Confirm:

- exactly one `README.md` remains
- `agents/openai.yaml` exists
- `references/BUILDER_PLAYBOOK.md` exists
- no `BUILDER_PLAYBOOK.template.md` remains
- no Template Usage Guide remains
- no references to a Template Usage Guide remain
- no `Project_Learnings.template.md` remains
- no instructions to create `Context/Project_Learnings.md` remain
- no `START_HERE_FOR_CODEX.md` remains
- no template-level README remains
- no Markdown or YAML file is executable
- ordinary skill files other than excluded `.DS_Store` metadata are `644`
- directories are `755`

## Final Pass 1 report

Report:

- original file tree
- final file tree
- files created
- files moved or renamed
- files deleted
- references updated
- permissions changed
- validation commands run
- unresolved structural issues

Do not begin Pass 2 until user confirms Pass 1 is complete.

---

# Pass 2 — Workflow Reconciliation

## Task

The structural cleanup from Pass 1 should already be complete.

This pass is limited to reconciling the skill’s workflow, instructions, templates, and human-facing README.

Do not install or symlink the skill. Do not run the temporary-project forward test yet.

Before editing:

1. Inspect the current file tree.
2. Read:
   - `SKILL.md`
   - `README.md`
   - `references/BUILDER_PLAYBOOK.md`
   - all active project-output templates
3. Identify contradictions, stale references, duplicated instructions, and references to deleted files.
4. Treat `SKILL.md` as the authoritative workflow.
5. Treat `README.md` as human-facing and GitHub-facing documentation, not a competing controller.

Prefer minimal edits to workflow sections that are already correct. Verify and
lightly refine existing activation, staged discovery, prototype-code, safety,
branching, and web-research rules rather than rewriting them merely to match
different wording.

## 1. Reconcile skill activation behavior

Confirm `SKILL.md` allows use through the following, and edit only if needed:

- explicit invocation with `$agentic-project-kickoff`
- a clear natural-language request for the complete project-kickoff workflow

Use behavior equivalent to:

```markdown
Use this skill when explicitly invoked or when the user clearly asks to turn an idea into a structured agentic software, application, automation, AI, or coding project.

Do not activate merely because the user mentions a possible idea, asks a narrow coding question, or requests a small isolated task.
```

Do not require exact-name invocation. Do not add a restrictive implicit-invocation policy to `agents/openai.yaml`.

## 2. Preserve the staged kickoff workflow

### Initial discovery

When the skill begins:

1. Ask the user to describe the project idea in whatever form they have.
2. Assess what is already clear.
3. Summarize the current understanding.
4. Identify the most important gaps.
5. Ask one adaptive question at a time.
6. Avoid endless interviewing.
7. Preserve unresolved matters as assumptions, risks, open questions, decisions needed, or human-review items.

### First document set

Create only:

- `Context/IDEA_INTAKE.md`
- `ProjectPlans/STRATEGY.md`
- `ProjectPlans/DECISIONS.md`
- `ProjectPlans/TASKS.md`

Then stop for human review.

The review response must summarize:

- files created or changed
- current project direction
- important assumptions
- unresolved questions
- decisions still needed
- recommendations
- what the human should correct
- recommended next step

`ProjectPlans/TASKS.md` must include `Open Questions / Decisions Needed`.

Each item should identify:

- the question or decision
- its source
- why it matters
- when it should be resolved
- whether it blocks the current phase

### Expanded workspace

After human approval, create or update:

- `AGENTS.md`
- `ProjectPlans/ROADMAP.md`
- `Context/Decision_Notes.md`
- `Context/3-Hop-Method.md`
- `docs/phase0/PHASE0_CHECKLIST.md`
- `docs/phase0/READINESS_ASSESSMENT.md`
- `docs/templates/`
- `z-archive/`
- `.gitignore` when appropriate

Do not create by default:

- `README.md`
- `BUILDER_PLAYBOOK.md`

A project README may be created when the user asks, the repository will be shared immediately, or external-facing documentation is needed.

A project-local Builder Playbook may be created only when the user explicitly requests it or a portable snapshot is needed.

Copy reusable phase templates into `docs/templates/`. These are reusable blank structures, not active project documents requiring immediate review.

If the user explicitly asks for the complete kickoff package in one pass, the skill may skip the intermediate review stop.

## 3. Reconcile the single-master Builder Playbook model

The one authoritative Builder Playbook is:

`references/BUILDER_PLAYBOOK.md`

The master playbook contains approved reusable practices that apply across projects.

Update `SKILL.md` so it tells Codex when to read this reference. Read it when
starting a kickoff where approved cross-project practices affect the work and
when comparing promotion candidates during a Project Retrospective. Do not copy
it into a project by default.

During normal project work:

- do not continuously edit the master playbook
- capture meaningful lessons in Phase Retrospective files
- preserve candidate transferable lessons for the Project Retrospective

At the Project Retrospective:

1. gather candidate lessons
2. combine duplicates
3. identify conflicts
4. distinguish project-specific lessons from transferable improvements
5. compare proposals against the current master Builder Playbook
6. show all proposed promotions together
7. wait for human approval
8. after approval, update as required:
   - `references/BUILDER_PLAYBOOK.md`
   - `SKILL.md`
   - relevant templates
   - `README.md`
   - `agents/openai.yaml`, when appropriate

Do not update the skill package lesson-by-lesson during normal project work.

The human may explicitly request immediate promotion of a specific lesson. In that case, show exact proposed files and changes, identify duplicate/conflict risks, obtain approval, then apply the approved changes.

## 4. Rewrite the Phase Retrospective template

Locate:

`assets/templates/docs/templates/PHASE_RETROSPECTIVE.template.md`

Revise it so it serves as both:

1. a running learning log during the phase
2. a conditional phase-end retrospective

Use a structure similar to:

```markdown
# Phase Retrospective

## Phase Information

- Phase:
- Status:
- Started:
- Completed:
- Retrospective status:
  - Running
  - Ready for review
  - Completed
  - Separate retrospective not needed

## Running Learning Log

For each meaningful entry:

- Date
- Observation
- Evidence or example
- Effect on the work
- Possible lesson
- Project-specific or potentially transferable
- Needs end-of-phase review: Yes / No

Do not record routine activity or trivial observations.

## End-of-Phase Review

- review all running-log entries
- add lessons identified during final review
- combine duplicates
- remove incorrect or unsupported observations
- clarify vague lessons
- identify conflicts
- distinguish project-specific lessons from potentially transferable lessons
- identify project documents or future work that should change

## Approved Phase Lessons

### Project-specific lessons

### Candidate transferable lessons

Do not promote candidates during a normal Phase Retrospective unless the human explicitly requests immediate promotion.

## Actions and Follow-up

- project documents to update
- tasks to create or revise
- unresolved issues to carry into `ProjectPlans/TASKS.md`
- lessons to present at the Project Retrospective
```

Update `SKILL.md` so substantive Phase Retrospectives are conditional.

Complete one when the phase involved meaningful learning, rework, bugs, design changes, tooling or environment issues, human-agent collaboration friction, major decisions, or reusable patterns.

Skip or compress it when the phase was small, straightforward, low-risk, and free of meaningful reusable lessons or process issues.

When skipped, Codex may record:

`No substantive phase retrospective was needed because no major reusable lessons or process issues were identified.`

The Phase Retrospective file may be created at the beginning of a phase or when the first meaningful lesson appears.

## 5. Strengthen the Project Retrospective template

Locate:

`assets/templates/docs/templates/PROJECT_RETROSPECTIVE.template.md`

The skill should recommend a Project Retrospective when a project appears complete, meaningfully wrapped, paused for an extended period, abandoned, or ready for a major restart or transition. It may also run whenever the user requests it.

The Project Retrospective must gather candidate lessons from:

- Phase Retrospectives
- review summaries
- human feedback
- recurring bugs
- rework
- important decisions
- failures
- successful patterns

It must:

1. consolidate similar lessons
2. remove one-off or project-specific observations that should not transfer
3. identify duplicate recommendations
4. compare proposals against `references/BUILDER_PLAYBOOK.md`
5. identify conflicts between proposed improvements
6. identify destination files
7. present all promotion recommendations together
8. wait for human approval
9. update approved skill files afterward

Include a promotion table similar to:

```markdown
| Candidate improvement | Promote? | Evidence | Destination files | Duplicate/conflict check | Human decision |
|---|---:|---|---|---|---|
```

Potential destination files include:

- `references/BUILDER_PLAYBOOK.md`
- `SKILL.md`
- relevant templates
- `README.md`
- `agents/openai.yaml`

## 6. Preserve flexible kickoff coding behavior

Do not create a rigid no-code rule.

During kickoff:

- prefer discovery, strategy, planning, and validation before implementation
- do not rush into production implementation
- allow small runnable code files, prototypes, sample scripts, proofs of concept, pseudocode, mock data, or examples when they materially help demonstrate feasibility, compare options, test assumptions, explain architecture, show a workflow, or support a better decision

Clearly label exploratory code as sample, prototype, proof of concept, or non-production.

Explain why it is useful, how to run it, what it demonstrates, and what it does not prove.

Avoid large production implementations or irreversible commitments during kickoff unless the human explicitly approves moving into implementation.

## 7. Reconcile safety and branching rules

Do not require a branch or recovery point for ordinary documentation-only kickoff work.

Require or recommend an appropriate safety step before implementation work, material changes to working code, database/schema changes, deployment configuration, destructive edits, or changes that could break a working project.

Possible safety steps:

- Git branch
- checkpoint commit
- backup
- explicit human approval

If a target project file exists, read it first, preserve useful content, merge carefully, and do not overwrite blindly.

Never overwrite source templates while generating a project.

Treat:

`skill package = source`

`project workspace = destination`

Approved retrospective promotion is the deliberate exception that permits edits to the skill package.

## 8. Reconcile web research behavior

Use web or current documentation when needed, when the user requests it, or when technical decisions depend on current facts.

Examples:

- API availability
- pricing
- OAuth requirements
- platform rules
- deployment limits
- software or library versions
- security guidance
- legal or compliance requirements
- official setup instructions

Prefer official documentation and primary sources.

Do not perform unnecessary web research for ordinary ideation when current facts do not materially affect the project.

## 9. Rewrite the root README

The root `README.md` is the only human-facing and GitHub-facing guide.

It should explain:

- what the skill does
- when to use it
- how to invoke it
- the staged workflow
- the initial four files
- the expanded workspace
- how templates are used
- the one-master Builder Playbook model
- Phase Retrospectives as running learning logs and conditional reviews
- Project Retrospective promotion
- flexible prototype/sample-code behavior
- installation by copying or symlinking
- basic validation and testing

Use:

```markdown
## Installation

Copy or symlink the complete `agentic-project-kickoff` folder into:

`~/.agents/skills/agentic-project-kickoff`
```

Do not make the README a second workflow controller. Point readers to `SKILL.md` as authoritative.

## 10. Add a concise template-library explanation to README

Distinguish:

### Initial project documents

- `Context/IDEA_INTAKE.md`
- `ProjectPlans/STRATEGY.md`
- `ProjectPlans/DECISIONS.md`
- `ProjectPlans/TASKS.md`

### Expanded project documents

- `AGENTS.md`
- `ProjectPlans/ROADMAP.md`
- `Context/Decision_Notes.md`
- `Context/3-Hop-Method.md`
- `docs/phase0/PHASE0_CHECKLIST.md`
- `docs/phase0/READINESS_ASSESSMENT.md`

### Reusable phase templates copied into projects

- `docs/templates/PHASE_PLAN.template.md`
- `docs/templates/PHASE_CHECKLIST.template.md`
- `docs/templates/REVIEW_SUMMARY.template.md`
- `docs/templates/PHASE_RETROSPECTIVE.template.md`
- `docs/templates/PROJECT_RETROSPECTIVE.template.md`

### Skill-only master file

- `references/BUILDER_PLAYBOOK.md`

Make clear that the master Builder Playbook is read and applied by the skill but is not copied into projects by default.

## Pass 2 consistency review

Search for stale references to:

- `Project_Learnings`
- `Project Learnings`
- `TEMPLATE_USAGE_GUIDE`
- `Template Usage Guide`
- `Template Guide`
- `BUILDER_PLAYBOOK.template`
- `assets/templates/BUILDER_PLAYBOOK`
- `assets/BUILDER_PLAYBOOK.md`
- `START_HERE_FOR_CODEX`
- `assets/templates/README.md`

Remove or correct all stale or contradictory references.

Confirm that `SKILL.md`, `README.md`, `references/BUILDER_PLAYBOOK.md`, and all retrospective templates describe one consistent workflow.

## Final Pass 2 report

Report:

- files materially revised
- workflow changes made
- contradictions removed
- stale references removed
- unresolved workflow questions
- recommendations for Pass 3 testing

Do not begin Pass 3 until user confirms Pass 2 is complete. 

---

# Pass 3 — Validation and Testing

## Task

Passes 1 and 2 should already be complete.

This pass is for inspection, validation, and controlled testing only.

Do not make broad redesign changes. Small corrections are allowed only when a validation failure identifies a specific defect. Report every correction.

Do not install the skill into `~/.agents/skills`. Do not create or modify a symlink. Do not test against a real project.

## 1. Inspect the final structure

Record the complete final file tree.

Confirm the package approximately resembles:

```text
agentic-project-kickoff/
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
├── references/
│   └── BUILDER_PLAYBOOK.md
└── assets/
    └── templates/
        ├── .gitignore.template
        ├── AGENTS.template.md
        ├── Context/
        │   ├── IDEA_INTAKE.template.md
        │   ├── Decision_Notes.template.md
        │   └── 3-Hop-Method.md
        ├── ProjectPlans/
        │   ├── STRATEGY.template.md
        │   ├── ROADMAP.template.md
        │   ├── DECISIONS.template.md
        │   └── TASKS.template.md
        └── docs/
            ├── phase0/
            │   ├── PHASE0_CHECKLIST.template.md
            │   └── READINESS_ASSESSMENT.template.md
            └── templates/
                ├── PHASE_PLAN.template.md
                ├── PHASE_CHECKLIST.template.md
                ├── REVIEW_SUMMARY.template.md
                ├── PHASE_RETROSPECTIVE.template.md
                └── PROJECT_RETROSPECTIVE.template.md
```

Additional files are acceptable only when they have a clear approved purpose.

## 2. Structural checks

Confirm:

- exactly one `README.md` exists
- `agents/openai.yaml` exists
- `references/BUILDER_PLAYBOOK.md` exists
- no `BUILDER_PLAYBOOK.template.md` remains
- no `START_HERE_FOR_CODEX.md` remains
- no template-level README remains

Use filesystem searches rather than visual inspection alone.

## 3. Permission checks

Run:

```bash
find /path/to/agentic-project-kickoff -type f -perm -111 -print
```

No Markdown or YAML file should appear.

Confirm ordinary files are `644`, directories are `755`, and only genuine executable scripts are `755`.

Check for world-writable files:

```bash
find /path/to/agentic-project-kickoff -type f -perm -002 -print
```

No ordinary skill file should appear.

Exclude `.DS_Store` files from permission normalization and treat them as
out-of-scope Finder metadata unless the human separately authorizes cleanup. Do
not change ownership.

## 4. Validate `agents/openai.yaml`

Confirm it contains:

```yaml
interface:
  display_name: "Agentic Project Kickoff"
  short_description: "Structure projects before implementation"
  default_prompt: "Use $agentic-project-kickoff to structure this project idea."
```

Confirm it does not contain:

```yaml
policy:
  allow_implicit_invocation: false
```

Validate YAML syntax using an available parser. Do not install packages unless the human explicitly approves.

## 5. Validate `SKILL.md` frontmatter

Confirm:

- the YAML frontmatter parses successfully
- `name` exists
- `description` exists
- `name` is `agentic-project-kickoff`
- no unsupported or unintended fields are present

Do not claim an official validator passed unless it actually ran.

## 6. Workflow consistency checks

Confirm `SKILL.md` clearly states:

- explicit and clearly matching natural-language invocation are allowed
- narrow coding questions do not trigger the skill
- discovery uses one adaptive question at a time
- unresolved uncertainty is preserved
- initial creation is limited to four documents
- Codex stops for review afterward
- full expansion occurs only after approval unless explicitly overridden
- no project README is created by default
- no project-local Builder Playbook is created by default
- Phase Retrospectives serve as running learning logs and conditional phase-end reviews
- the Project Retrospective is the consolidated promotion gate
- human approval is required before modifying the global skill package
- approved improvements may update the master playbook, `SKILL.md`, and relevant templates
- prototype/sample code is allowed when useful
- kickoff should not rush into production implementation
- branches or recovery points are used at implementation-risk points, not ordinary documentation-only kickoff
- web research is used as needed or when requested

Confirm the root README does not contradict these rules.

## 7. Reference checks

Search for:

- `Project_Learnings`
- `Project Learnings`
- `TEMPLATE_USAGE_GUIDE`
- `Template Usage Guide`
- `Template Guide`
- `BUILDER_PLAYBOOK.template`
- `assets/templates/BUILDER_PLAYBOOK`
- `assets/BUILDER_PLAYBOOK.md`
- `START_HERE_FOR_CODEX`
- `assets/templates/README.md`

Review every result and correct only clearly stale or contradictory references.

Search for references to files that do not exist.

Confirm every skill-source path named in `SKILL.md` exists and every generated-project path is clearly identified as a destination.

## 8. Behavioral validation and temporary kickoff test

Keep deterministic validation separate from behavioral forward-testing.
Structure, permissions, YAML, frontmatter, references, and template consistency
can be validated directly. A static simulation by the same agent does not fully
validate activation or whether the skill reliably guides a fresh agent.

Before launching an independent fresh-agent forward test, request explicit
human authorization for that test. If authorization is not given or true
isolated invocation is unavailable, perform the closest valid static simulation,
mark behavioral results `Partially tested`, and state that activation and
fresh-context behavior were not validated.

Create a disposable temporary project directory outside the skill source folder.

Use a simple sample idea:

`Build a personal household inventory app that lets one user record items, locations, purchase dates, and warranty information.`

If an independent forward test is authorized, use a fresh isolated context and
provide only the skill source and a natural user-style request. Do not reveal
the expected output file list, suspected defects, or intended answers to the
test agent. Compare the resulting artifacts with the checklist below only after
the test agent finishes.

Otherwise, test the kickoff workflow as closely as the environment permits
through static inspection or a dry run.

Simulate or invoke:

`$agentic-project-kickoff`

Confirm the skill:

1. asks for or accepts the project idea
2. summarizes what is clear
3. identifies meaningful gaps
4. asks one adaptive question at a time
5. avoids unnecessary interrogation
6. creates only:
   - `Context/IDEA_INTAKE.md`
   - `ProjectPlans/STRATEGY.md`
   - `ProjectPlans/DECISIONS.md`
   - `ProjectPlans/TASKS.md`
7. stops for human review
8. reports assumptions and unresolved decisions
9. includes `Open Questions / Decisions Needed` in `TASKS.md`
10. does not create the full workspace
11. does not create a project README
12. does not create a project-local Builder Playbook
13. does not modify source templates
14. does not modify `references/BUILDER_PLAYBOOK.md`

If true independent invocation is unavailable, perform the closest valid dry
run or static simulation, mark it `Partially tested`, and state the limitation
clearly. Do not claim that skill activation or fresh-agent behavior passed.

## 9. Expansion-gate test

Using only the disposable test project, simulate human approval to expand the workspace.

Confirm the next stage creates or proposes:

- `AGENTS.md`
- `ProjectPlans/ROADMAP.md`
- `Context/Decision_Notes.md`
- `Context/3-Hop-Method.md`
- `docs/phase0/PHASE0_CHECKLIST.md`
- `docs/phase0/READINESS_ASSESSMENT.md`
- `docs/templates/`
- `z-archive/`
- `.gitignore` when appropriate

Confirm it does not create by default:

- `README.md`
- `BUILDER_PLAYBOOK.md`

Confirm reusable phase templates are placed under `docs/templates/`.

Confirm the skill source files remain unchanged.

## 10. Phase Retrospective template checks

Inspect:

`assets/templates/docs/templates/PHASE_RETROSPECTIVE.template.md`

Confirm it includes:

- phase information
- retrospective status
- running learning log
- evidence or examples
- effect on the work
- project-specific versus potentially transferable classification
- end-of-phase cleanup
- approved phase lessons
- candidate transferable lessons
- actions and follow-up
- conditional and lightweight behavior

Confirm it does not instruct Codex to update a separate Project Learnings file.

## 11. Project Retrospective template checks

Inspect:

`assets/templates/docs/templates/PROJECT_RETROSPECTIVE.template.md`

Confirm it includes:

- candidate collection from Phase Retrospectives and review artifacts
- duplicate consolidation
- conflict analysis
- comparison with `references/BUILDER_PLAYBOOK.md`
- destination-file identification
- a consolidated human-approval table
- post-approval skill-update instructions

Confirm it does not reference a Template Usage Guide or Project Learnings file.

## 12. Source-protection check

Record file hashes or modification times before the temporary test for:

- `SKILL.md`
- `references/BUILDER_PLAYBOOK.md`
- `assets/templates/`

After the test, confirm those source files were not modified.

Approved promotion is not part of this test.

Use hashes only as before/after evidence for source protection during this test.
Do not describe them as permanent integrity enforcement or skill authorization.

## 13. Cleanup

Delete the disposable temporary test project after recording results, unless the human asks to preserve it.

Do not delete or modify the actual skill package.

## Final Pass 3 report

### Validation performed

- structure checks
- permission checks
- YAML validation
- frontmatter validation
- reference checks
- workflow consistency review
- temporary kickoff test
- expansion-gate test
- retrospective template checks
- source-protection check

### Results

For each check, mark:

- Passed
- Failed
- Partially tested
- Not testable in the current environment

### Corrections made

List every file changed during testing and the exact reason.

### Remaining limitations

State anything that could not be tested, including whether the skill was tested
by an authorized independent fresh agent or only statically simulated.

### Final readiness assessment

State whether the package is:

- ready for installation
- ready with minor caveats
- not ready

Do not install or symlink the skill.
