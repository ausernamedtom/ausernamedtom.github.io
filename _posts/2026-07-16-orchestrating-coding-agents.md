---
layout: post
title: "Orchestrating Coding Agents - From One Loop to Many"
date: 2026-07-16
categories: [Agentic Development]
tags: [ai, agents, orchestration, agentic-development, automation]
---

In [Pang - The Table Tennis Simulator](/posts/pang-the-table-tennis-simulator/) I wrote about _Agent Developed Software_ (ADS): handing an agent control over development, from specs to code. That experiment left me with a nagging follow-up question: **what happens when you run more than one?**

Because here's the thing: a single autonomous coding loop is no longer the hard part. An agent that picks up a task, writes code, runs the tests and opens a pull request is quickly becoming table stakes. The hard part starts the moment you want **many of those loops running at the same time**. Suddenly the interesting questions are:

- Who decides what gets worked on next?
- Where does each agent do its work?
- What happens when the world changes while an agent is busy?
- Who checks the results, and when?

None of these questions are answered _inside_ a coding loop. They need a layer **above** the loops: an **orchestration layer**. Think of it as a conductor. The conductor doesn't play an instrument, yet without one, a stage full of excellent musicians produces noise instead of music.

Below are the four responsibilities I keep running into, and the lessons they taught me. All concepts, no code, promise. 🤓

## 📋 Scheduling: The Backlog Is the Program

Once agents do the typing, the backlog stops being a wishlist and becomes **the actual interface between your intent and the machines executing it**. The orchestration layer pulls work from that backlog and decides:

- **What runs next**, based on priority and dependencies
- **How much runs in parallel**, because two tasks touching the same area of a codebase will happily sabotage each other
- **When to stop**, because an agent that is stuck should not burn hours pretending otherwise

The uncomfortable consequence: **writing good issues becomes the highest-leverage skill you have**. The issue _is_ the prompt. A vague issue no longer produces a confused colleague asking questions, it produces confident nonsense at machine speed.

## 🧳 Isolation: Every Task Gets Its Own Workspace

The first thing that breaks when you run multiple agents is the workspace. Agents sharing one working copy will step on each other: half-finished changes bleeding together and files switching underneath each other's feet.

The pattern that works is simple: **every task gets its own isolated workspace**, a fresh copy of the project where the agent can make a mess without consequences for anyone else.

- **Failures become disposable.** If a task goes sideways, you throw away the workspace, not the project.
- **Parallelism becomes safe.** Ten agents on ten tasks never touch each other's work-in-progress.
- **It enforces discipline.** If a task can't be completed inside one self-contained workspace, it was probably too entangled to hand to an agent (or a human) in the first place.

## 🔄 Reconciliation: Trust the Source of Truth, Not the Agent

Long-running autonomous loops **drift**. An agent dies halfway through a task. A pull request gets merged, or rejected, while nobody was looking. An issue changes after the work already started. The longer the system runs, the further its picture of the world wanders from reality.

The lesson I borrowed from infrastructure tooling: **an agent's memory is not the record**. The backlog and the repository are. The orchestration layer should continuously compare the _desired state_ (what the backlog says) with the _observed state_ (what the repository and open pull requests say), and repair the difference:

- A task claimed by an agent that disappeared? Release it.
- A pull request that got merged? Mark the work done.
- A workspace nobody is using anymore? Clean it up.

Drift isn't an incident, it's the **default condition** of a system like this. Reconciliation is what makes it boring instead of catastrophic.

## 🔍 Review: The Loop Doesn't End at the Pull Request

It's tempting to consider the job done when the pull request appears. In practice, that's where _your_ job starts. Merging is the approval, and no amount of automation should quietly take that away.

But the more interesting review happens **over time**, across many outcomes:

- Do agents keep tripping over the same missing convention? That's a rule waiting to be written, the same lesson the [development rules](/posts/pang-the-table-tennis-simulator/) taught me at project scale.
- Are certain kinds of tasks consistently fine, and others consistently messy? That tells you what to delegate more of, and what to keep close.
- **Trust is earned in layers.** Start by reviewing everything. Let routine work flow faster only after it has proven itself boring.

## 🧑‍🎓 Lessons So Far

1. **Your job moves up a level.** From writing code to writing intent, and from reviewing lines to reviewing outcomes.
2. **Guardrails beat supervision.** You cannot watch ten loops at once. Encode your expectations in rules and boundaries instead of hovering.
3. **Boring machinery wins.** A dull, reliable backlog-and-workspace routine beats a clever prompt every single time.
4. **Don't orchestrate what doesn't need it.** At [Future Tech 2026](/posts/future-tech-2026-my-take-aways/) the warning was "don't use agents where simple automation works". The same applies one level up: don't run a fleet where one loop, or a plain old script, will do.

## 💡 Final Thoughts

The pattern repeats at every scale. Vibe coding taught me that an unstructured agent produces chaos in a single file. ADS taught me that specs and rules turn that chaos into a maintainable project. Orchestration is the same lesson again, one level higher: **structure is what turns raw agent capability into something you can actually rely on**.

The conductor still doesn't play an instrument. Increasingly, neither do I, and figuring out how to conduct well has turned out to be the most interesting engineering problem I've worked on in years.

Curious what breaks next. Stay tuned. 🎼

Disclaimer: this post is about orchestrating coding agents, so it is only fitting that an agent helped write it 🤖🤓
