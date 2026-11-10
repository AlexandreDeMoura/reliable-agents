---
title: "Getting agents to do reliable, production-scale work: opinions, behavior specs, forking and simulations"
type: conference_talk_transcript
speaker:
  name: Mitch
  role: Co-founder
  organization: Basis
  location: New York City, USA
company_focus: AI agents that do end-to-end work for accounting firms
speakers_count: 1
language: en-US
duration: "~00:19:05"
transcript_start: "00:00:12"
transcript_end: "00:19:04"
timestamp_format: "[HH:MM:SS] = moment the segment starts in the original recording"
source: automatic speech-to-text export (HTML), cleaned up
cleanup_level: clean verbatim (speaker's wording kept; obvious speech-to-text errors fixed; see "Transcription notes")
visuals: the talk refers to slides that are NOT included in this file
---

# Transcript: Basis talk on reliable agents in production

## How to use this file (for AI agents)

- The **Summary** and **Key concepts** sections are written by the editor, not spoken by the speaker. Treat them as a guide only.
- The **Transcript** section is the speaker's own words. Quote from there.
- Each line starts with a timestamp `[HH:MM:SS]` showing where it begins in the recording.
- Words in `[square brackets]` inside the transcript were added or corrected by the editor. The list of corrections is in **Transcription notes** at the end.
- When the speaker says "this image" or "one slide back", he is pointing at a slide that is not available here.

## Summary

Mitch, co-founder of Basis (a New York company building AI agents for accounting firms), argues that individuals have become far more productive with AI agents, but companies have not, because agents today are hard to use well and don't follow a shared, consistent way of working. He compares agents to employees: companies trust non-perfect people by putting **processes** in place, and agents need the same thing.

His main points:

1. **Two gaps** stop agents from being used across a company:
   - a **competency gap**: the user has to manage the agent's setup (skills, memory, when to use it), which most knowledge workers can't do;
   - a **process gap**: each user shapes the agent their own way, so the work is done differently each time, which goes against how companies scale (process should come from leadership).
2. To be **simple and reliable**, an agent must be **opinionated**: it should do things the same way every time. Reliability means consistency, not perfection.
3. Opinions come from expert intuition and first principles (e.g. "read the primary source before answering", "take a picture of the slide deck before returning it"). Opinions also cover trade-offs like speed, not just accuracy.
4. Three tools to check and improve agent behavior beyond classic right-or-wrong tests:
   - **Behavior specs**: a written list of the most important intended behaviors, used both to align the team and as a grading checklist on any run, even runs with no known correct answer.
   - **Forking**: copying an agent run at the exact moment it made a bad decision, then asking it why or changing its instructions to see if the behavior changes.
   - **Simulations**: asking a coding agent to create a helper agent that role-plays the production agent, to quickly test instruction changes cheaply.
5. Being opinionated is what makes it possible to "close the loop" and build agents that improve themselves in production. Trust grows over time, like with a new coworker.
6. Closing answer to "won't agents find better ways if left free?": maybe, but that's not how you scale real economic output. Nobody wants surprising creativity in their tax return.

## Key concepts (editor's plain-language definitions)

| Term used in the talk | What it means |
|---|---|
| Agent | An AI system that carries out multi-step tasks on its own, using tools. |
| Production / production scale | Real use with real customers, at high volume, as opposed to testing. |
| Non-deterministic | Doesn't always give the same output for the same input (true of people and of AI models). |
| Competency gap | The user must have special skill to set up and steer the agent; most workers don't. |
| Process gap | No shared, top-down way of doing the work; each user customizes the agent differently. |
| Opinionated (agent) | Built to follow chosen, fixed ways of doing things, so results are consistent. |
| Eval | A test used to measure how well an agent performs. |
| Outcome-based eval | A classic test with a known right answer and a grading rubric; judges only the final result. |
| Ground truth | The known correct answer used to grade a result. |
| Trajectory | The full record of one agent run: every step, decision, and tool use from start to finish. |
| Generalize to production | Whether good test results actually carry over to the much larger variety of real cases. |
| Distribution (of trajectories) | The full spread of situations the agent will face in real use. |
| Behavior spec | A short written list of the most important behaviors the agent must show; used as a team agreement and as a grading checklist. |
| Behavior eval | Checking a run against the behavior spec: was the behavior relevant here, and did the agent show it? |
| Context / "runtime training data" | Everything the agent reads while working (instructions, tool descriptions, extra guidance shown step by step). The speaker says to treat it as data that shapes behavior, not just a "prompt". |
| System prompt | The main standing instructions given to the agent. |
| Progressive disclosure | Showing the agent extra instructions only when they become relevant. |
| Harness | The software around the AI model that runs the agent (tools, instructions, environment). |
| Forking | Making an exact copy of an agent run at a chosen point, to replay or question it. |
| Simulation | A cheaper stand-in for forking: a helper agent pretends to be the production agent so you can test changes. |
| Sub-agent | A helper agent created by another agent. |
| Inference call | One single request to the AI model; a long run can make about a thousand. |
| Signal | Useful evidence about whether the agent is behaving well. |
| Close the loop | Using that evidence to automatically improve the agent, so it gets better over time. |
| Bank reconciliation / plug | Matching a company's records with its bank statement; a "plug" is a made-up entry that forces numbers to match without explaining the difference. |
| VLM | Vision-language model: an AI model that can read images, such as scanned PDFs. |
| OCR | Optical character recognition: turning images of text into actual text. |
| PR / blast radius | A proposed code change for review; "blast radius" is how much could break if it's wrong. |
| "Move 37" | A reference to AlphaGo's famously unexpected, creative move against Go champion Lee Sedol (2016). Here it means an agent inventing a surprising new approach. |

---

## Transcript

### 1. Introduction

[00:00:12] I'm Mitch, I'm one of the two co-founders of Basis.

[00:00:15] We are based right here in New York City.

[00:00:18] And the thing that we focus on is: how can we get agents to do production-scale work reliably enough so that we are trusted and used end to end by accounting firms across the country?

[00:00:35] So a lot of our research really focuses on this. How can you get these agents to do real work in the real economy?

### 2. The productivity puzzle

[00:00:42] So I wanted to start with a question, which is: if you're at this conference — honestly, if you're watching this talk — you've probably increased your own productivity a lot.

[00:00:54] You're using [Cowork] or Codex or [Claude Code], whatever it is.

[00:00:59] And yet if you go look at the average company, the average enterprise, the average firm, their productivity hasn't gone up [10x or 100x].

[00:01:10] I know my productivity has gone up maybe 100x, but if I were working inside of a company, it wouldn't be anywhere near that order of magnitude.

[00:01:18] And so why is that? At [Basis], one of the things we [think] is that humans are already used to working with non-deterministic systems.

[00:01:28] It's just that those systems are normally their coworkers, not their computers.

### 3. How organizations build trust: process

[00:01:32] And so I actually think, to answer this question, it's useful to look at the existing way we organize intelligent systems, which are companies.

[00:01:41] So how do organizations scale their own productivity? Right.

[00:01:47] Organizations need to trust that the people doing the work at every level are doing it in a way that is going to produce good outputs consistently, at scale. Right.

[00:02:00] If you look at this image here — here I'm showing an accounting firm, but this could honestly be any company, any traditional company — the person who is the head of the firm has literally no idea about the exact day-to-day of the people at the very bottom, and yet somehow they trust the work is going to be done.

[00:02:19] How does that happen? It doesn't happen through deterministic systems, right? People are not deterministic; they're intelligent entities.

[00:02:28] And instead there are processes that are put in place.

[00:02:30] And if the people who put those processes in place believe that those processes will generalize to good outcomes, then they can be comfortable that, even though the people executing the processes are not deterministic, you will generally get to good outcomes if you follow the process.

[00:02:49] This is true, by the way, even in the smallest step.

[00:02:51] So in this example, earlier I was showing the process of maybe doing the monthly accounting work for a client at an accounting firm.

[00:02:58] But even within that process, say you wanted to see: how do I reconcile the cash? Well, I need to get the bank data, and even that has a process.

[00:03:09] The process could be really basic. It could just be "use the approved software to extract the data", but there's still some process that is put in place.

[00:03:17] You wouldn't expect the firm to just let anyone Google "extract PDF" and then use whatever they want to go extract it, because now you wouldn't have process.

### 4. Why today's agents fall short: two gaps

[00:03:29] Now let's talk about agents.

[00:03:33] As I said earlier, everyone already has [Cowork], [ChatGPT], Codex, [Claude Code]. But where is that productivity?

[00:03:41] Our opinion is that there are two main problems that come with what I'll call coding-style agent products, where you're working with an agent and you just tell it to go and do something.

[00:03:52] **The first problem is that there is a competency gap**, because agents, as you think of them today, outsource the complexity to the user, right?

[00:04:04] If I'm the user, I now have to manage my skills and memories and think about when I should use the agent.

[00:04:14] I have to have, like, theory of mind over it. This is really hard.

[00:04:18] And for those who are here, maybe you guys find it easy. But I promise, the wide majority of knowledge workers have no idea how to do any of this.

[00:04:29] **And there's a second problem, which is that there is a process gap.**

[00:04:33] If you are saying, "Hey user, here's your agent, do what you want," well, now suddenly the way the work is being done is essentially up to whoever the user is, to customize the way the agent is behaving.

[00:04:47] Which goes entirely against the way that organizations scale. The process should be coming from senior leadership down, not from any individual employee deciding how they're going to do their job.

[00:05:01] Going back to my example: if every employee just made up their own way of extracting data, even back in 2018, that wouldn't be a good outcome.

[00:05:10] And so we believe strongly that the key to building production agents that actually do work end to end is solving these two problems.

[00:05:17] You have to solve the competency gap by making it simple for the user.

[00:05:22] You shouldn't even have to know it's an agent — or you probably know, but you don't have to really feel it.

[00:05:28] It should be as simple as just working with a coworker. You just tell it what you want it to do, and all the things you expect to work should work.

[00:05:35] And the second is the process gap. You need agents to be reliable.

[00:05:40] Not having a stroke of genius one day and forgetting something the other day. It's not about being perfect.

[00:05:47] Humans are not perfect. Nobody is looking for perfect.

[00:05:51] What you're looking for is reliability and consistency and trustworthiness.

### 5. Being opinionated

[00:05:57] So what does that actually look like?

[00:06:00] We think that to be simple and reliable — where you don't have to think about what to do, and the agent can just do it for you without the user knowing the internals — to do it the same way consistently, you have to be opinionated.

[00:06:21] Just to show you, actually, one slide back here: I want to point out that getting it right isn't actually the main task, because, again, humans are imperfect themselves.

[00:06:30] The point is to do it in a similar way. It's OK if you can't do something, but you should be consistently able to not do something.

[00:06:37] And then you get better, just like a person does.

[00:06:40] If you suddenly one day are an expert at something and then you miss something very basic — [and that is] expected — now you're not reliable. So you have to be opinionated.

[00:06:51] What's an example of an opinion?

[00:06:54] Let's say we have an agent and we need to go and read a PDF. So in this case, what is the best way to read a PDF? There are many ways.

[00:07:02] You can throw it into a [VLM]; you can go write some Python code and use an open-source OCR library.

[00:07:08] You can work with one of the many OCR-specific model providers.

[00:07:13] You could do an OCR and then have the VLM check its work; you could have 5 VLMs check its work; you could run 100 VLMs and take the best [one].

[00:07:21] There are literally infinite possibilities here.

[00:07:24] And so if you just said, "Agent, extract [this] PDF," you might get good outcomes, but they're not going to be reliable.

[00:07:34] Not because the process it used was bad, but because the process it used was not opinionated.

[00:07:40] So the way we develop opinions is: you use initial intuition, write some things down; you know what the opinions are from expertise.

[00:07:53] For example, if I'm trying to get an agent to do tax work, well, I know that if the agent goes and reads the primary source before returning to the user, it is more likely to be accurate.

[00:08:07] I don't need a set of evals to tell me that. I don't need to divine that. From first principles, we know that, right?

[00:08:13] I know that if I have an agent make a PowerPoint, if it takes a picture of the PowerPoint before it returns to the user, it is less likely to have a formatting issue. That is obvious.

[00:08:24] And so there are lots of different ways to develop opinions.

[00:08:27] And one of the key points I'll make here, before I move on to talking about how to use the opinions, is that the opinions are not just about accuracy.

[00:08:34] We just talked about the example of taking a picture of a PowerPoint. Well, that adds latency. You may not want that.

[00:08:41] Maybe the agent only makes formatting mistakes one out of 100 times, and you're OK with that. And so therefore you don't have to take a picture of the PowerPoint.

[00:08:50] The point isn't exactly what the opinion is.

[00:08:52] In this talk we're going to focus not on how to develop opinions, but on why opinions matter, and then how to leverage those opinions as you scale in production.

### 6. The limits of outcome-based evals

[00:09:02] So say you have a bunch of outcome-based evals.

[00:09:09] And when I say outcome-based evals, I mean what we traditionally think of as evals. It has a right answer. We have some rubric. We're trying to judge what the agent did.

[00:09:18] How do you know that the evals generalize to production? Right.

[00:09:23] You might have 1,000 cases or 100 cases, but in production, if you're doing real work, you're going to have hundreds of thousands of situations. And that's only going to increase as the trajectories get much longer, because the number of decisions made in a trajectory grows exponentially as the long-horizon abilities of agents increase.

[00:09:47] And so, if you look at this whole distribution of trajectories, you can't prove every one of them is correct.

[00:09:54] You don't have a ground truth for every trajectory, but you can potentially look at the trajectories to decide if the behavior — the opinion that you have — is actually happening.

[00:10:06] And you can then infer that if it's actually happening, it's more likely to generalize to production.

[00:10:12] It's not perfect. You don't know for sure. But at least you are having an opinion.

### 7. Worked example and behavior specs

[00:10:18] Let's run through a real example.

[00:10:20] So let's say that, for a behavior — this is an accounting example — if there is a mistake in the bank reconciliation, I don't want the agent to post an entry to make it tie.

[00:10:40] I want it to explain the difference instead of putting in a plug.

[00:10:44] Again, this is subjective. You might want it the other way. But what you don't want is a customer getting one way one time and another way the other time.

[00:10:53] And so what would you do?

[00:10:54] Well, first let's understand what governs the behavior, right?

[00:10:58] The behavior is not just "you write two sentences into your system prompt and it just works".

[00:11:03] That might work for very simple, narrow agents, but when you're building these big agent systems, the behavior is actually a combination of the system prompt, your progressive disclosures, potentially tool descriptions, and obviously the training of the model.

[00:11:16] There are a lot of things that govern the behavior.

[00:11:19] And one tip, by the way, that I have for when you think about building production agents: really think of the context the agent is going to see as it's working as runtime training data.

[00:11:30] The word "prompt", I think, trivializes it a bit.

[00:11:32] At the end of the day, this is the data that the agent is being trained on at runtime. Not literally trained on, but effectively trained on.

[00:11:40] And so you want to care about every single token and understand how that data causes the agent to behave in certain ways.

[00:11:48] So when you're putting behaviors into production, it might be very difficult to put one behavior into exactly one paragraph, because, again, the behavior is all of the data at once, not just one thing.

[00:12:03] And one of the things that we developed at Basis was this concept of a **behavior spec**.

[00:12:10] The idea of a behavior spec is essentially saying: before we update the context or write the context, what do we actually want? What is our intent?

[00:12:22] You can think of this a little bit as a product spec for the agent era.

[00:12:26] And you shouldn't be writing every detail down. You should be taking the most important opinions and putting them in.

[00:12:31] Because if you put everything, you'll be duplicating a lot of statements. So you define the ones that matter the most, and then you can use them as a way to align your team from a spec perspective.

But you can also use them as a rubric, and you can say, in this particular trajectory — in any trajectory that doesn't have a ground truth — did the agent get into a situation in which this behavior was relevant?

[00:12:58] And then, if so, did it actually exhibit the behavior?

[00:13:02] And this way you can scale the amount of signal that you get on your trajectories far more than by trying to increase the number of outcome-based evals, ground truths and rubrics that you have.

[00:13:13] Those are really important, but you cannot scale them anywhere near as fast as you can scale the ability to supervise what the behaviors of the agent are.

### 8. Forking

[00:13:24] Let's just take a quick example here.

[00:13:28] So this is actually the second concept, which is called **forking**, which we can combine here with behavior specs.

[00:13:36] If you've ever used [Claude Code] or Codex — the web apps — there's this little fork icon under each message, where you can essentially create a copy of the agent at that same point. That works really well on your local computer.

[00:13:48] It's hard to build a system that does this perfectly in the cloud, with recreating the database environment, recreating the prompts, all the different environments.

[00:13:59] But forking is another tool that we use to check the behavior.

[00:14:02] So a flow you can imagine is: we had a trajectory — it could be in an eval set, it could be in testing or in production — and it failed the behavior eval.

[00:14:14] And now you're trying to understand why. Why did it fail the behavior eval? Why did it not exhibit the behavior?

[00:14:20] Then you can go into the trajectory, to right before the point where the agent made the decision that went against the behavior — assuming, again, that your behavior is good and is actually producing signal.

[00:14:33] And then you can fork it.

[00:14:35] When you fork it — you would fork it right after it made the decision — you could ask it, "Why'd you do that?"

[00:14:44] Or you can go and update the context of the agent and see if that improves the behavior.

[00:14:50] And so with forking, you can start to actually take the signal and figure out how to improve the harness to leverage that signal. Whereas if you're just looking at signal from an outcome-based eval, you can't do that anywhere near as easily: it failed the outcome — how do you know why?

[00:15:09] You have potentially 1,000 inference calls in your five-hour-long agent trajectory run.

### 9. Simulations

[00:15:15] And then we have the last tool that I'd like to talk about — this is actually my favorite — which is called **simulations**.

[00:15:21] It's going to sound silly, but I promise it's very effective: when working with coding agents, you can ask the coding agent to make a sub-agent that pretends to be your production agent.

[00:15:33] And you have to tell it not to cheat.

[00:15:35] And you give it the system prompt and the skills, and you render everything perfectly.

[00:15:38] And what you can do is ask it what it would do in some scenario.

[00:15:41] You can give it the situation and see if you can recreate the situation that you saw in your eval or in production.

[00:15:46] And if you can recreate it, now you can tweak your context to see if the sub-agent would change its behavior based on your tweaks.

[00:15:54] This is obviously far worse than forking.

[00:15:58] But you do it because it's much cheaper and much faster, and it allows you, as an agent builder, to really easily iterate on ideas without going through the expensive process of actually forking an entire environment.

[00:16:12] And you can do this today without building anything. I did this for literally a year and a half plus before we built forking into our internal systems.

[00:16:23] And the main idea here is understanding that, if you look at the distribution from earlier, you're trying to understand whether the agent is generalizing to that distribution. And you don't know that purely from the outcome evals.

[00:16:39] You can try to choose the best outcome evals that should represent the curve, but you don't really know.

[00:16:43] And so supervising the process through some of these methods gives you some additional signal to understand whether it's generalizing properly.

### 10. Closing the loop and earning trust

[00:16:52] And what's really cool is that as you start to get more signal, you can actually start to close the loop.

[00:17:01] And an opinion is what lets you do that, right? If you don't have opinions about your system, you're not going to be able to develop that signal.

[00:17:10] And now, if we go back to the firm: we talked about supervision; we talked about how the agent, if you're opinionated, can be more reliable and can actually be simpler.

[00:17:21] What that means is: maybe the agent starts by doing something narrow, but because people work with it, they see that it's easy to use and they see that it's reliable. As it gets better and better, they will trust it more.

[00:17:33] It's just like a coworker. You have a coworker. Day one, imagine someone sends you a PR.

[00:17:39] You're looking at two axes. Whenever someone sends you a PR, it's these two axes: what's the blast radius of the PR, and what is your perception of how competent that person is? Right.

[00:17:51] If the least competent person at your company sends you a PR that edits a core service, you're going to look at it.

[00:17:58] If the most competent person sends you a PR that edits an internal document, you may not look at it as much.

[00:18:04] And this idea is true with humans working with agents, just like it is with humans working with humans.

### 11. Closing: why not let agents find their own way?

[00:18:10] And I just want to make one last point to close, which is that one of the things people say when they hear this is: "Well, wait a second — if you let the agents just do what they want, won't they find a better way? Won't they 'move 37' your work?"

[00:18:23] And the answer is: they might. But that's not how you scale GDP and scale output.

[00:18:30] Nobody wants their agent to "move 37" their tax return.

[00:18:35] They want it done reliably, at scale, in a way that they can trust, and in a way that their people know how to work with.

[00:18:42] And so the thing I will leave you with is: for agents to [diffuse], they need to be simple. For teams to trust them, they need to be reliable.

[00:18:50] The way you get to simple and reliable is that you develop opinions on how the agent behaves. And then opinions allow you to build that additional signal that helps you start to close the loop, so that you can achieve self-improving agents in production.

[00:19:04] Thank you very much.

---

## Transcription notes (editor's corrections)

The original was an automatic speech-to-text export. Very short fragments ("Right.", "So.") were merged into neighboring lines; filler words were lightly trimmed. Content and meaning were not changed. Specific fixes:

| Timestamp | Original text | Corrected to | Reason |
|---|---|---|---|
| 00:00:54, 00:03:33 | "cloud code" | Claude Code | Product name misheard |
| 00:00:54, 00:03:33 | "cowork" | Cowork | Product name capitalization |
| 00:03:33 | "chatgpt" | ChatGPT | Product name capitalization |
| 00:00:59 | "10x100x" | 10x or 100x | Merged numbers |
| 00:01:18–00:01:22 | "And so why is that at Basis? One of the things we have is…" | "And so why is that? At Basis, one of the things we [think] is…" | Sentence break misplaced; "have" likely "think" |
| 00:06:40 | "…miss something very basic, that would be expected. Now, you're not reliable…" | "…miss something very basic — [and that is] expected — now you're not reliable…" | Sentence split; meaning: if that happens, you are not reliable |
| 00:07:02 | "vlm" | VLM | Acronym |
| 00:07:13 | "take the best event" | take the best [one] | Probably misheard ("best of N") |
| 00:07:24 | "agent, extract, Excel, extract PDF" | "Agent, extract [this] PDF" | Speaker stumbled |
| 00:10:20 | "if there is a mistake for in the bank reconciliation" | "if there is a mistake in the bank reconciliation" | Stray word |
| 00:12:31 | "duplicating a lot of states" | duplicating a lot of statements | Likely misheard |
| 00:13:36 | "Claude code", "Local computer" | Claude Code, local computer | Capitalization |
| 00:13:48 | "in the cloud provider perfectly" | perfectly in the cloud | Grammar |
| 00:18:42 | "for agents to defuse" | for agents to [diffuse] | Misheard: means "spread widely through the economy" |
| 00:18:21, 00:18:30 | "move 37" | kept as-is | Intentional reference to AlphaGo's 2016 move (see Key concepts) |
