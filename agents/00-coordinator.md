# Agent 00 — Coordinator

## Inputs and reading
Read SKILL.md and its knowledge-base routing table. Inspect the current request, prior answers and any existing user files. Read only the active stage's detailed references unless a question requires more; the entire knowledge base remains available to every role.

## Steps
1. Classify creation, redesign, migration, repair or skill maintenance. For an edit, a repair or a snippet, every stage follows [editing an existing theme](../references/editing.md) in place of its new-theme steps. A bare invocation in a new request means create a theme and start the business/project interview. An edit request with an XML path means inspect that file and establish the requested changes first; preserve an ongoing task when context already establishes one. Do not start a theme interview during skill maintenance. Confirm the actual requested scope from available evidence.
2. Choose the final destination: normally the user's current project directory, containing one descriptively named XML for this run. Do not create a persistent theme-output directory. Plan naming after the interview. Preserve any user's original theme unchanged.
3. Keep the brief, a design.md-format plan, selected-resource notes and test results in conversation or a private temporary area outside the final project. No persistent workflow-state, brief, handoff or manifest files are required. Track only temporary paths created by this run so cleanup is safe.
4. Execute stages 01–07 in order. Assign an agent only when the bounded task can run alongside useful independent work; otherwise use its instructions yourself. Supply the role path, relevant actual inputs, revision and owned writable paths. Never claim an independent review happened when it did not.
5. For each handoff, require: inspected inputs/revision; output or current decision; facts with evidence; assumptions/unknowns; checks marked pass/fail/not_run; and the specific next action. Check that evidence applies to the actual revision. A statement from another agent is not proof by itself.
6. Ask user-facing questions only through the coordinator. Required missing answers stay pending; optional preferences can use explicit assumptions. Do not turn elapsed waiting into an answer. Questions are plain English and multi-choice, with free responses always welcome.
7. Send defects to their owning stage. Brief changes invalidate affected design/resources/XML/tests; resource or XML changes invalidate affected tests. Reuse only evidence with unchanged inputs. Never loop on an unchanged error without a new hypothesis.
8. Finish with the single XML and a short English explanation of installation, tests actually run and concrete limits. No extra approval checkpoint is imposed by this skill. Account changes still require the user's actual authorization.

## Completion and boundaries
The final file exists, matches the current brief, has no unresolved required local defects, and no run-created sidecars remain in the destination. Missing Blogger access may leave live checks not_run with explicit disclosure. Do not claim installation, production certification or universal library support. Never delete user inputs or unrelated artifacts during cleanup.
