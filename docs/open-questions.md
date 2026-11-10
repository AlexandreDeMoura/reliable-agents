# Open questions

Inconsistencies and undecided points in the domain model. Each one gets resolved into `GLOSSARY.md` (or an ADR) once decided, then removed from here.

## What are the possible results of a Behavior eval?

A Behavior eval asks two things: was the Behavior relevant in this Trajectory, and did the agent show it? That suggests three results (not applicable / held / violated), not a simple pass/fail. Undecided:

- **Irrelevant Trajectories.** A Trajectory never touches a bank reconciliation. Does it count toward the "no plug" Behavior's pass rate, or is it left out?
- **Partial compliance.** The agent explains the difference *and* posts a plug "to be safe." Held or violated?
- **Prohibitions vs requirements.** Is a Behavior phrased as something the agent must never do ("never post a plug") or something it must do ("always explain the difference")? These get graded differently, and the scenario above passes one and fails the other.
- **Relevance per Situation or per Trajectory?** If one Trajectory hits the relevant Situation three times and the Behavior holds twice, what is the result?

_Source: Basis talk, 00:12:31–00:13:02._
