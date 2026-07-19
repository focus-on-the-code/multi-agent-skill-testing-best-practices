Multi-Agent Skill Testing

A lightweight method for testing whether an agent skill reliably guides a fresh agent—not whether a reviewer can infer the intended behavior.

Setup

Use three roles:

Agent 1: test conductor and simulated project owner
Fresh child: system under test
Agent 2: independent live reviewer

Run everything in a disposable workspace. Keep expected outputs, scoring criteria, and suspected defects hidden from the child.

Test Flow
Record source hashes and the empty project baseline.
Launch Agent 2 before the child starts.
Give the child only the skill, project path, and a natural user request.
Capture questions, responses, files, hashes, and timestamps at each gate.
Approve only the next authorized stage.
Have both agents write independent reports.
Verify findings, compare source hashes, and clean up.
Results

Label each requirement as Passed, Failed, Partially tested, Not testable, or Not applicable.

Preserve raw evidence. A final report or final directory alone is not enough to prove staged behavior.
