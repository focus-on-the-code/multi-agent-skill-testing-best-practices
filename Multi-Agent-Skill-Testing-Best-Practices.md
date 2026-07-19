# Multi-Agent Skill Testing Best Practices

## Purpose

This guide describes a reusable method for testing agent skills with multiple independent agents. It applies to skills that guide planning, coding, document creation, tool use, staged workflows, or other agent behavior.

The goal is to determine whether a skill reliably guides a fresh agent—not whether a reviewer can infer the intended behavior from the skill after seeing the expected answer.

These are testing practices. They should not create extra administrative work for humans using the skill normally.

## Contents

1. [Core principles](#core-principles)
2. [Recommended agent structure](#recommended-agent-structure)
3. [Role definitions](#role-definitions)
4. [Test design](#test-design)
5. [Model and context controls](#model-and-context-controls)
6. [Evidence to preserve](#evidence-to-preserve)
7. [Recommended test sequence](#recommended-test-sequence)
8. [Agent 1 report requirements](#agent-1-report-requirements)
9. [Agent 2 review requirements](#agent-2-review-requirements)
10. [Evaluation standards](#evaluation-standards)
11. [Common failure modes](#common-failure-modes)
12. [Reusable orchestration prompts](#reusable-orchestration-prompts)
13. [Completion checklist](#completion-checklist)
14. [Example reports](#example-reports)

## Core principles

### Separate the project owner from the system under test

The agent using the skill should not also evaluate its own work.

Use one agent as the test conductor and simulated project owner. That conductor should launch a separate fresh child agent as the system under test.

### Protect the fresh context

Initially give the system-under-test agent only:

- the skill path or installed skill;
- a natural user-style request;
- a disposable project location;
- safety boundaries required for the test.

Do not initially disclose:

- the expected output files;
- the validation checklist;
- suspected defects;
- intended answers;
- previous conclusions;
- another reviewer’s report.

### Test behavior before reviewing instructions

The conductor may know the project-owner brief and test boundaries, but the child should demonstrate the workflow before being shown evaluation criteria.

Compare observed behavior with the skill only after the behavioral run reaches its intended stopping point.

### Separate deterministic checks from behavioral evidence

Deterministic checks can directly establish facts such as:

- file presence;
- directory structure;
- permissions;
- YAML or frontmatter parsing;
- stale references;
- source hashes;
- exact template copying.

Behavioral tests establish different facts:

- whether a fresh agent activates the skill;
- whether it asks useful questions;
- whether it honors review gates;
- whether it avoids premature work;
- whether it handles uncertainty;
- whether its summaries and decisions are coherent.

A static review cannot fully replace a fresh behavioral test.

### Preserve raw evidence

Reports are interpretations. Raw artifacts, transcripts, trees, and hashes are evidence.

An independent reviewer should be able to determine which claims are directly verified, supported only by the conductor’s narrative, or not testable from retained evidence.

### Keep test controls separate from skill requirements

A test may impose controls such as a maximum of five discovery questions to bound time and cost. That does not make the cap a requirement of the skill.

Score the skill against its actual instructions. Report test-specific limits separately.

## Recommended agent structure

```text
Human
└── Primary orchestrator
    ├── Agent 1: test conductor and simulated project owner
    │   └── Fresh child: system under test using the skill
    └── Agent 2: live independent observer and evidence reviewer
```

This structure uses three distinct reasoning roles:

1. the primary orchestrator controls scope, safety, evidence, and completion;
2. Agent 1 conducts the test and interacts with the child as the project owner;
3. Agent 2 watches the test in parallel, independently captures gate evidence, and later checks Agent 1's report and raw artifacts.

When concurrency permits, launch Agent 2 before the child begins. A typical live test uses four active slots: primary orchestrator, Agent 1, Agent 1's child, and Agent 2.

## Role definitions

### Human

The human:

- authorizes the test and any external effects;
- defines model, cost, and safety constraints;
- approves the final interpretation or further changes.

The human should not need to simulate every project-owner response or manually create the test report.

### Primary orchestrator

The primary orchestrator:

- selects the disposable workspace;
- records the pre-test source baseline;
- gives Agent 1 a bounded project brief and answer bank;
- launches Agent 2 as a live observer before the behavioral run;
- confirms Agent 1 launches a separate fresh child;
- monitors progress without feeding expected answers to the child;
- confirms both reports are written;
- independently checks important claims;
- compares pre-test and post-test source state;
- cleans up disposable artifacts;
- issues the final readiness assessment.

### Agent 1: conductor and simulated project owner

Agent 1:

- launches the fresh child using the required model settings;
- provides the child with a natural user request;
- answers discovery questions naturally from a small answer bank;
- enforces any test-specific question cap without representing it as a skill rule;
- records the interaction;
- notifies Agent 2 at each gate before authorizing continuation;
- observes review and approval gates;
- inspects artifacts at each gate;
- approves only the next authorized stage;
- writes the behavioral test report.

Agent 1 must not silently perform the child’s work or supply the expected artifact list.

### Fresh child: system under test

The child:

- receives the skill and user-style request;
- follows the skill as a normal agent would;
- asks the project owner for information when needed;
- creates artifacts only within the disposable workspace;
- stops at required gates;
- does not receive evaluation criteria until the behavioral run is complete.

### Agent 2: independent reviewer

Agent 2:

- begins before the child starts;
- reads the complete skill;
- records the disposable-project and source baseline;
- watches the conductor and child workflow in parallel without steering the child;
- captures independent trees, hashes, timestamps, and gate observations before continuation;
- derives expected behavior independently;
- reads Agent 1’s report;
- inspects raw test artifacts;
- verifies claims instead of assuming they are correct;
- separates skill defects from test-method limitations;
- writes an independent assessment.

Agent 2 should not edit the skill, Agent 1’s report, or the raw project.

Agent 2 cannot automatically see another agent's private conversation. The orchestration must provide a neutral event channel. At minimum:

1. Agent 1 sends the exact child response and a gate-reached notification to Agent 2;
2. Agent 2 inspects and records the filesystem before Agent 1 sends approval;
3. Agent 1 sends the exact approval message and time;
4. Agent 2 records when expansion first becomes observable.

This event channel informs the reviewer about what occurred but does not expose expected answers to the child.

## Test design

### Choose a representative but bounded scenario

The scenario should be realistic enough to exercise the skill’s central workflow but small enough to complete safely.

A good test brief identifies:

- target user;
- desired outcome;
- initial scope;
- material constraints;
- privacy or safety boundaries;
- success criteria;
- a few reasonable preferences.

Avoid supplying details that the skill should discover itself.

### Prepare a project-owner answer bank

Give Agent 1 a small set of facts it can use when the child asks questions. The answer bank prevents the conductor from inventing inconsistent requirements during the run.

If a question is not covered, Agent 1 should:

1. make a low-risk project-owner choice;
2. state it simply;
3. ensure unresolved uncertainty remains visible.

### Bound discovery without contaminating evaluation

When time or cost must be controlled, set a test-specific maximum number of discovery questions.

After the cap:

- Agent 1 tells the child it has enough information;
- the child proceeds with documented assumptions and open questions;
- the report states that the cap was a test constraint.

Do not mark the skill down merely because it asked fewer questions than the cap.

### Exercise meaningful gates

For a staged skill, test at least:

1. initial activation and discovery;
2. the first artifact set;
3. the human-review stop;
4. the approval that unlocks the next stage;
5. the expanded or completed result;
6. any explicit prohibition, such as avoiding production work.

Do not approve later stages prematurely.

### Use a disposable destination

Never test a skill’s generative workflow against a real project unless the human explicitly authorizes it.

The disposable destination should be:

- outside the skill source;
- empty or baseline-recorded;
- writable by the system under test;
- removable after evidence is captured.

## Model and context controls

### Record model configuration

Record the requested model and reasoning level for:

- Agent 1;
- Agent 1’s child;
- Agent 2;
- any additional reviewer.

If the runtime does not expose the actual resolved model, distinguish:

- requested configuration;
- confirmed configuration;
- configuration not independently observable.

### Use fresh contexts

Use fresh contexts for the child and Agent 2.

Do not fork the full orchestration history into an independent reviewer when that history contains:

- expected results;
- suspected bugs;
- the primary agent’s conclusions;
- hidden scoring criteria.

### Keep settings consistent when comparison matters

Use the same model and reasoning level across agents when the goal is to compare role behavior rather than model capability.

Use different settings only when model variation is itself part of the test.

## Evidence to preserve

### Before the behavioral run

Record:

- logical skill tree;
- source-file hashes or a hash manifest;
- permissions when relevant;
- disposable-project baseline tree;
- pre-existing metadata files;
- model and reasoning assignments;
- initiating prompt.

Hashes are before-and-after evidence, not permanent integrity enforcement.

### During the run

Preserve:

- the child’s questions;
- Agent 1’s answers;
- user-facing child responses;
- approval messages;
- a tree or manifest at every important gate;
- hashes of artifacts that may later be overwritten;
- any skipped or unavailable checks;
- agent identities and relationships.

For staged workflows, the initial tree must be captured before expansion. A final tree cannot prove what existed earlier.

Agent 2 should capture this evidence live and independently. Agent 1's own snapshot remains useful, but matching snapshots from the conductor and observer provide stronger timing evidence.

### After the run

Record:

- final project tree;
- important artifact contents;
- unexpected and prohibited files;
- post-test source hashes;
- exact comparison result;
- cleanup result;
- report paths.

### Preserve evidence proportionally

Testing evidence should be detailed enough for independent review. This does not mean normal users must document every review step.

The extra rigor belongs to the testing process, not the everyday human workflow.

## Recommended test sequence

### 1. Define the test contract

Document:

- skill under test;
- authorized behavior;
- prohibited behavior;
- disposable destination;
- report destinations;
- model settings;
- discovery-question cap, if any;
- cleanup policy.

### 2. Run deterministic prechecks

Inspect structure, metadata, syntax, references, permissions, and source hashes before behavioral testing.

Do not install dependencies solely to make a validator run unless the human approves.

### 3. Launch Agent 1

Tell Agent 1 that it is the conductor and project owner, not the system under test.

Require Agent 1 to launch a separate fresh child.

### 4. Launch Agent 2 as a live observer

Before the child begins, give Agent 2:

- the skill source;
- the disposable project path;
- the test boundaries;
- the observer report path;
- the neutral gate-notification protocol.

Agent 2 should derive expected behavior from the skill, capture source and project baselines, then wait for live gate events. Do not give Agent 2 the primary orchestrator's intended conclusion.

### 5. Launch the child with minimal context

Give the child the skill, disposable path, and natural request. Do not disclose expected artifacts.

### 6. Conduct discovery

Agent 1 answers one question at a time from the answer bank. It records each exchange and stops at the configured cap.

Agent 1 also sends exact questions and answers to Agent 2's observation channel so the reviewer has a contemporaneous record.

### 7. Capture the first gate

Before approving expansion or continuation:

- record the child’s response;
- notify Agent 2 that the gate has been reached;
- have Agent 1 and Agent 2 independently capture the tree;
- have Agent 2 record timestamps and hash or copy the initial artifacts when later stages may overwrite them;
- note unexpected or missing artifacts.

Do not continue until Agent 2 confirms that its initial-gate snapshot is complete.

### 8. Approve the next stage narrowly

The approval should state what is authorized.

Approval to expand documents should not silently approve proposed project decisions, production implementation, deployment, spending, or source modification.

Send the exact approval message to Agent 2. The reviewer should record the approval time and when new expansion artifacts first become observable.

### 9. Capture the completed stage

Record the final response, tree, artifact contents, and any remaining gates.

Agent 2 should independently capture the final tree and hashes before reading Agent 1's conclusions.

### 10. Have Agent 1 write its report

Agent 1 may read the full skill and expected behavior after the run is complete. It should compare observations with the source and label unsupported claims.

### 11. Have Agent 2 finalize its review

After Agent 1's report exists, Agent 2 reads it and compares its claims with Agent 2's live observation log, gate snapshots, and raw artifacts. Agent 2 then writes the independent review.

### 12. Perform the primary audit

The primary orchestrator:

- reads both reports;
- verifies key findings;
- checks source hashes;
- resolves disagreements;
- assigns final result statuses.

### 13. Clean up

Delete only the disposable project after reports and evidence are safely retained, unless the human requests preservation.

Never delete the skill source or historical reports as part of test cleanup.

## Agent 1 report requirements

Agent 1’s report should include:

- purpose and scope;
- requested model settings;
- conductor and child identities;
- exact initiating prompt;
- chronological questions and answers;
- question count and any test cap;
- first review response;
- initial tree and artifact manifest;
- expansion or continuation approval;
- final response;
- final tree;
- missing, unexpected, and prohibited artifacts;
- checks performed and skipped;
- source-protection evidence;
- observed strengths;
- deviations and defects;
- test limitations;
- pass, fail, partial, or not-testable result.

Agent 1 should distinguish:

- what it directly observed;
- what it inferred;
- what it could not prove.

## Agent 2 review requirements

Agent 2’s report should include:

- requested model settings;
- sources inspected;
- independently derived expected behavior;
- live observation method and event channel;
- baseline, initial-gate, approval, and expansion timestamps;
- independent initial and final trees or manifests;
- verification of Agent 1’s claims;
- evidence available for each stage;
- unsupported or unreproducible claims;
- artifact defects;
- skill defects;
- test-method defects;
- recommended skill improvements;
- recommended testing improvements;
- untested branches;
- independent readiness assessment.

Agent 2 should use language such as:

- **Verified:** supported by raw evidence.
- **Supported but not independently reproducible:** plausible and documented by Agent 1, but raw evidence is incomplete.
- **Not proven:** insufficient evidence.
- **Contradicted:** raw evidence conflicts with the claim.

## Evaluation standards

Use consistent result labels.

| Result | Meaning |
|---|---|
| Passed | Direct evidence shows the requirement was satisfied. |
| Failed | Direct evidence shows the requirement was violated. |
| Partially tested | Some behavior was exercised, but evidence or coverage is incomplete. |
| Not testable | The environment could not exercise or validate the requirement. |
| Not applicable | The requirement does not apply to the tested scenario. |

### Separate package readiness from branch coverage

A skill may be ready with minor caveats even when some optional branches were not exercised.

Do not claim unconditional behavioral reliability from a single successful scenario.

### Separate skill defects from generated-artifact defects

A generated inconsistency may result from:

- unclear skill guidance;
- a weak template;
- an agent reasoning error;
- ambiguous project-owner approval;
- incomplete test evidence.

Identify the most likely cause and confidence level before recommending a skill change.

## Common failure modes

### Agent 1 is incorrectly made the system under test

If Agent 1 directly uses the skill, it cannot independently act as both project owner and evaluator.

Fix: Agent 1 launches a separate fresh child.

### Expected outputs leak into the child prompt

The child may reconstruct the expected answer instead of demonstrating general behavior.

Fix: give only the skill, project path, and natural request initially.

### A final snapshot is treated as proof of staged behavior

A final directory cannot prove that expansion occurred only after approval.

Fix: launch Agent 2 before the child and have it independently capture immutable trees, manifests, hashes, and timestamps at each gate before Agent 1 authorizes continuation.

### Agent 2 is launched only after testing

A post-test reviewer can inspect final artifacts but cannot independently verify when files appeared.

Fix: make Agent 2 a live observer. It should capture the baseline, initial gate, approval event, and expansion event in parallel, then read Agent 1's report afterward.

### Agent reports replace raw evidence

A detailed narrative can still be incomplete or mistaken.

Fix: retain transcripts, trees, hashes, commands, and artifacts.

### A test-specific rule is scored as a skill rule

For example, a five-question testing cap may not exist in the skill.

Fix: derive expected behavior from the skill and label orchestration controls separately.

### Broad approval is overinterpreted

Approval to continue one workflow stage may be treated as approval of every proposed decision.

Fix: make approvals narrow and preserve unapproved decisions as proposed.

### Git is unavailable

Lack of a Git repository does not prevent source-protection testing.

Fix: use a before-and-after hash manifest.

### Sub-agents stall without observable work

Do not wait indefinitely.

Fix:

1. check the live agent state;
2. check the disposable filesystem for progress;
3. set a bounded no-progress window;
4. interrupt a genuinely stalled turn;
5. restart with a new fresh agent or report the orchestration failure;
6. do not classify a pre-execution runtime stall as a skill failure.

### Normal human workflow becomes burdened by test rigor

Independent testing needs more evidence than ordinary use.

Fix: keep detailed evidence requirements in the testing process. Normal users should review decisions and results, not maintain test logs.

## Reusable orchestration prompts

### Agent 1 conductor prompt outline

```text
Act as the independent test conductor and simulated project owner.

Skill:
[SKILL PATH]

Disposable project:
[PROJECT PATH]

Report:
[AGENT 1 REPORT PATH]

Spawn a fresh child using [MODEL AND REASONING]. Initially give the child
only the skill, project path, and this natural request:

"[USER-STYLE REQUEST]"

Answer discovery questions one at a time from the supplied answer bank.
Answer no more than [CAP] questions. After the cap, direct the child to
proceed with documented assumptions.

Capture evidence at every gate. Approve only the next authorized stage.
Notify the live reviewer at every question, response, and gate. Do not
continue past a gate until the reviewer confirms its snapshot is complete.
After the run, inspect the skill and raw artifacts and write the report.
Do not modify the skill or delete the disposable project.
```

### Agent 2 reviewer prompt outline

```text
Act as a live independent observer and evidence reviewer.

Read:
- the complete skill at [SKILL PATH]
- raw artifacts at [PROJECT PATH]

Begin before the child starts. Derive expected behavior from the skill and
record source and project baselines. Watch the test through the neutral
event channel. At each gate, independently record the tree, hashes,
timestamps, and relevant exact messages before Agent 1 continues.

After the behavioral run, read Agent 1's report at [REPORT PATH]. Verify
its claims against your live observations and raw evidence. Separate skill
defects from test-method limitations.

Do not modify the skill, Agent 1's report, or raw artifacts.
Write your assessment to [AGENT 2 REPORT PATH].
```

## Completion checklist

### Setup

- [ ] Human authorization obtained.
- [ ] Skill source identified.
- [ ] Disposable project created.
- [ ] Report paths reserved.
- [ ] Agent models and reasoning levels specified.
- [ ] Project-owner answer bank prepared.
- [ ] Test-only discovery cap recorded, if used.

### Pre-test evidence

- [ ] Skill tree recorded.
- [ ] Source hashes recorded.
- [ ] Permissions and metadata checked when relevant.
- [ ] Disposable-project baseline recorded.

### Behavioral test

- [ ] Agent 1 launched as conductor.
- [ ] Agent 2 launched as live observer before the child.
- [ ] Agent 2 recorded source and disposable-project baselines.
- [ ] Separate fresh child launched as system under test.
- [ ] Child received no expected checklist.
- [ ] Discovery transcript captured.
- [ ] Agent 2 received contemporaneous discovery and gate events.
- [ ] Initial gate response captured.
- [ ] Agent 1 and Agent 2 independently captured the initial tree.
- [ ] Agent 2 captured initial hashes and timestamps before continuation.
- [ ] Approval scope recorded.
- [ ] Agent 2 recorded the approval time and first observable expansion.
- [ ] Final response and tree captured.
- [ ] Prohibited or unexpected outputs checked.

### Independent review

- [ ] Agent 1 report completed.
- [ ] Agent 2 inspected the complete skill.
- [ ] Agent 2 inspected raw artifacts.
- [ ] Agent 2 compared Agent 1's report with its live observation log.
- [ ] Agent 2 identified unsupported claims and untested branches.
- [ ] Agent 2 report completed.

### Final audit

- [ ] Primary orchestrator read both reports.
- [ ] Pre-test and post-test source hashes compared.
- [ ] Disagreements reconciled.
- [ ] Corrections, limitations, and readiness reported.
- [ ] Disposable project removed or intentionally preserved.
- [ ] Historical reports retained.

## Example reports

The following reports illustrate this method and several of its lessons:

- [Agent 1 behavioral test report](./Agentic-Project-Kickoff-Fresh-Agent-1-Test-Report.md)
- [Agent 2 independent review](./Agentic-Project-Kickoff-Fresh-Agent-2-Review.md)
- [Three-pass testing handoff](./Agentic-Project-Kickoff-Three-Pass-Handoff.md)

The examples show why:

- the conductor and system under test should be different agents;
- the independent reviewer should watch the run in parallel rather than relying only on final artifacts;
- a discovery-question cap must be labeled as a test constraint;
- initial and final evidence should both be retained;
- source hashes are useful when Git is unavailable;
- broad workflow approval should not be treated as approval of every proposed decision;
- independent review can identify both genuine skill issues and limitations in the test itself.
