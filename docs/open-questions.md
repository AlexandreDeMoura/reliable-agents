# Open questions

Inconsistencies and undecided points in the domain model. Each one gets resolved into `GLOSSARY.md` (or an ADR) once decided, then removed from here.

## Is a Behavior phrased as a prohibition or a requirement?

A Behavior can say what the agent must never do ("never post a plug") or what it must always do ("always explain the difference"). Already decided: an agent that explains the difference *and* posts a plug has violated the Behavior (see **Behavior eval** in the glossary). Still undecided: should a Behavior spec phrase each Behavior one way, both ways, or does it not matter?

_Source: Basis talk, 00:12:31–00:13:02._

## Where does an Opinion live: enforced by the Harness, or delivered as Context?

The dmp-courtage pilot holds some Opinions in tool code (ids, date formats, sort order) and delivers others to the agent as words. The glossary has no term for the difference. The case study writes "enforced by the Harness" and "delivered as Context". Still undecided: does the lab need two terms, and does **Behavior** only cover the second kind?

_Source: dmp-courtage case study, chapter 13 of the Opinionated Agents site._

## Does Context include tool results and earlier Trajectories?

**Context** is defined as "everything the agent reads while it works", then lists standing instructions, tool descriptions and guidance revealed along the way. In the pilot, the Behavior that broke was shaped by search results piling up in a thread shared by a whole day of Trajectories. The case study treats both as Context. Still undecided: should the definition name them?

_Source: dmp-courtage case study, chapter 15._

## What is one Trajectory when several share a thread?

The pilot keeps one thread per sender per day. Each batch of messages starts one agent run, which reads every earlier run of the day. The case study treats each batch as one **Trajectory**, so a **Behavior eval** gives one result per batch. Still undecided: confirm this, or treat the whole day as the Trajectory.

_Source: dmp-courtage `deploy/n8n.md`, part 7; case study, chapters 12 and 16._

## A name for a scripted set of Outcome evals

dmp-courtage calls its day of expected replies and register rows a "scénario", a word on **Situation**'s _Avoid_ list. The case study says "scripted day". Still undecided: does the lab need a term for a set of Outcome evals played in order on prepared data?

_Source: dmp-courtage `deploy/scenario.md`; case study, chapter 14._

## Two meanings of "Simulation"

dmp-courtage's own glossary defines Simulation as the yearly saving of an offer against the prospect's current bill. The lab's **Simulation** is a helper agent playing the production agent. The case study avoids the first meaning and writes "a savings figure". Still undecided: the English word to use when a later case study covers proposals.

_Source: dmp-courtage `GLOSSARY.md`; case study, chapter 18._

## "Case study" and the Avoid list

"case" is on **Situation**'s _Avoid_ list, while `CLAUDE.md` and the site call this kind of content a "case study". The site uses "case study" as a fixed phrase and nowhere else. Still undecided: record the exception in the glossary, or pick another name.
