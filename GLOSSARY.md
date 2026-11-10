# Reliable Agents Lab

A lab for understanding how to build agent harnesses and reliable agentic workflows. The vocabulary starts from two foundational talks (Basis on reliable agents in production; LangChain on when to build your own harness) and grows from there.

## Language

### Shaping the agent

**Opinion**:
A deliberately chosen way of doing something, decided by people and written down so the agent does it the same way every time.
_Avoid_: rule, preference, policy

**Behavior**:
An Opinion as it shows up in a Trajectory: something an observer can check for.
_Avoid_: action, habit

**Context**:
Everything the agent reads while it works: standing instructions, tool descriptions, and guidance revealed along the way.
_Avoid_: prompt, runtime training data

**Harness**:
The software around the model that runs the agent and assembles and delivers its Context.
_Avoid_: framework, wrapper, scaffold

**Process**:
A fixed way an organization gets consistent outcomes from non-deterministic people. Reserved for the human-organization analogy; the agent-side equivalent is an Opinion. The fixed phrase Process supervision is the only exception.

### Observing the agent

**Trajectory**:
The full record of one agent run, from start to finish: every step, decision, and tool use.
_Avoid_: run (as a noun), trace, transcript, log

**Situation**:
A circumstance the agent is in when a decision comes up. One Trajectory passes through many Situations; production is the full spread of Situations the agent will face.
_Avoid_: case, scenario, example

**Ground truth**:
The known correct result for a given input, used to grade an outcome.
_Avoid_: golden answer, label

### Checking the agent

**Process supervision**:
Checking how the agent worked across a Trajectory, rather than only what it produced. A fixed phrase covering Behavior specs, Forks, and Simulations; the counterpart to Outcome evals.
_Avoid_: behavior supervision, trajectory review

**Outcome eval**:
A check of the final result of a Trajectory against Ground truth.
_Avoid_: outcome-based eval, test, benchmark

**Behavior spec**:
A short written list of the most important Opinions an agent must hold, used both to align a team and as a grading checklist.
_Avoid_: product spec, rubric

**Behavior eval**:
A check of one Trajectory against one Behavior from the Behavior spec, without needing Ground truth. It gives one result per Trajectory: not applicable, held, or violated; a single break anywhere in the Trajectory makes it violated.
_Avoid_: process eval, pass/fail

**Judge**:
A model that performs Behavior evals on Trajectories and cites evidence from them for each result.
_Avoid_: grader, evaluator

**Fork**:
A copy of a Trajectory taken at a chosen point, from which you can question the agent about its decision or change its Context and let it continue.
_Avoid_: branch, replay, checkpoint

**Simulation**:
A helper agent role-playing the production agent with the same Context, used as a cheaper, rougher stand-in for a Fork.
_Avoid_: mock agent, dry run

**Signal**:
Evidence about whether the agent is behaving as intended.
_Avoid_: feedback, metric

### Adopting the agent

**Steering burden**:
The skill a user needs to get good work from an agent: managing its Context and knowing when to use it. Most users can't carry it, so a good agent removes it.
_Avoid_: competency gap

**Opinion gap**:
The lack of shared Opinions when each user shapes the agent their own way, so the same work comes out differently from user to user.
_Avoid_: process gap
