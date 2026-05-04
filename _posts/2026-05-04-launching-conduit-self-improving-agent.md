---
layout: post
title: "Launching Conduit – A Pluggable Take on OpenAI's Symphony Spec"
date: 2026-05-04
categories: [Agent Development, Vibe Coding]
tags: [conduit, symphony, aider, qwen, ai, agents, self-improving, github-issues]
---

> ⚠️ **Placeholder post** – this is an early write-up to mark the launch of **Conduit**. Expect rough edges, missing screenshots, and TODOs scattered throughout. I'll flesh it out once the dust settles.

## 🪦 Where Vibe Coding Took Me

I've spent the past year on vibe coding and what I started calling [_Agent Developed Software_](https://tomhofman.dev/posts/pang-the-table-tennis-simulator/) – first with [The Blue Car Game](https://tomhofman.dev/posts/vibe-coding-let-me-talk-you-through/), then [Baby Simulator](https://github.com/ausernamedtom/baby-simulator), then [Pang](https://github.com/ausernamedtom/pang). Each one taught me something. Each one also, eventually, **ran aground**.

The pattern was depressingly consistent:

- Early sessions felt magical. The agent shipped features faster than I could review them.
- A few weeks in, the codebase had quietly turned into a **less-than-ideal heap**: duplicated helpers, contradictory abstractions, dead branches the agent forgot it left behind.
- Maintenance and pruning – the boring parts – fell back to me. Which meant I was **back in the chat window**, doing exactly the work I'd hoped to offload.

The chat-driven loop is great for the _first_ hundred prompts. It's terrible for the _next_ thousand. Anything that lives only in a conversation evaporates the moment the conversation ends, and that's where the rot comes from.

So my goal with Conduit is plain: **offset the chat to issues and tasks**. If the next unit of work isn't written down somewhere durable, it shouldn't be done.

## 🎼 Conduit is an Implementation of OpenAI's Symphony Spec

Conduit didn't appear out of nowhere. It's an implementation of [**OpenAI's Symphony spec**](https://openai.com/) – the orchestration model that splits agent work into a **tracker** (what should be done), a **runner** (who does it), and a **conductor** (the loop that keeps them honest and learning).

What Symphony gets right, in my opinion:

- It treats the **tracker as ground truth**, not the chat history. Tasks live somewhere queryable.
- It separates **planning** from **execution** so each can be evaluated on its own merits.
- It assumes the system will **learn from outcomes**, not just produce them.

What I wanted to change:

> Symphony, as written, is a bit prescriptive about which tracker and which runner you bring. Conduit is a **pluggable** implementation with a deliberately **"limitless" choice of tracker and runner**.

Concretely, that means:

- **Tracker adapters** – GitHub Issues today; Jira, Linear, Trello, GitLab, plain Markdown TODOs, even an RSS feed of bug reports tomorrow. Anything that can express "here is a discrete unit of work" can plug in.
- **Runner adapters** – Aider + Qwen2.5-Coder is my default; but Claude Code, Cursor's agent, OpenAI's Codex CLI, a remote shell wrapped in a script – all valid runners as long as they can accept a task and return a diff (or a PR, or a patch).
- **A thin conductor** – Conduit itself is small on purpose. It hands a task from a tracker to a runner, watches the result, and writes a reflection note. That's it.

The point isn't to be clever. The point is that I never want to be _locked_ into one tracker or one model again, because both halves of that stack are moving too fast.

## 🚇 The Default Stack: GitHub Issues + Aider + Qwen2.5-Coder

For the launch, the default wiring is:

- **Tracker:** [GitHub Issues](https://docs.github.com/en/issues) – ubiquitous, free, already where my work lives.
- **Runner:** [Aider](https://aider.chat/) for git/diff/PR mechanics, driving [Qwen2.5-Coder](https://github.com/QwenLM/Qwen2.5-Coder) locally for the actual code generation.
- **Conductor:** Conduit, reading issues, dispatching to the runner, capturing outcomes.

A few honest reasons for those defaults:

- **Aider already does the boring parts well** – repo maps, diff application, commit messages, conflict handling. No reason to rebuild any of that.
- **Qwen2.5-Coder runs locally** – I can iterate on Conduit without burning API credits every time the loop misbehaves. And it misbehaves a lot in the early days.
- **GitHub Issues is where the work is anyway.** If a task isn't in an issue, Conduit shouldn't know about it.

But none of those choices are load-bearing. Swap the runner for Claude Code, swap the tracker for Linear, and Conduit shouldn't care.

## 🔁 The Self-Improving Loop

The loop is intentionally boring. No magic. No reinforcement learning rigs. Just a feedback file the runner reads before every task.

```text
   Tracker (Issues)  ──►  Conduit  ──►  Runner (Aider + Qwen2.5-Coder)
          ▲                                          │
          │                                          ▼
      New Tasks                              Pull Request / Patch
          │                                          │
          └────── Reflection Notes  ◄────  CI / Review Outcome
```

Each outcome turns into a short reflection note. Things like:

- _"When the issue mentions `_config.yml`, run `bundle exec jekyll build` before opening the PR."_
- _"Issues labeled `flaky-test` should never be closed without re-running the suite three times."_
- _"Don't touch `_posts/*.md` front matter unless the issue explicitly says so."_

Those notes live in a `CONDUIT.md` file the runner loads as context on the next task. Over time the playbook fills with hard-won lessons, and the system stops repeating the same mistakes.

This is the part that addresses the _heap of code_ problem from earlier projects: the lessons no longer live in a chat I closed last week. They live next to the code, in a file the next run will read.

## 🐛 Walking Through a Task

Imagine an issue like:

> **Title:** Sidebar avatar is blurry on retina displays
> **Body:** `assets/img/tom256.jpg` is only 256px. On 2x screens it looks fuzzy. Replace with a 512px version and update references if needed.
> **Labels:** `bug`, `good-first-issue`

What Conduit does:

1. **Tracker adapter** picks it up because `good-first-issue` has a high priority score and the body is short and concrete.
2. **Conduit** spins up the runner against a fresh worktree, with the issue body as the initial prompt and `CONDUIT.md` preloaded.
3. **Aider + Qwen2.5-Coder** propose the change, apply the diff, run the build, and push a branch.
4. A PR opens. CI runs. If it passes and gets approved, Conduit logs `outcome: merged` and moves on.
5. If CI fails, Conduit appends the failing log and asks the runner to try again, up to N retries, before tagging the issue `needs-human` and stepping away.

Every `needs-human` tag is a learning opportunity. When I fix it manually, Conduit reads the diff I wrote and adds a reflection note describing what it would have done differently. That note becomes part of the next run's context.

## 🚧 What's Not Working Yet

I'd rather over-share early than oversell.

- **Issue scoping is fragile.** A vague issue produces a vague PR. I want a pre-flight step where Conduit asks clarifying questions on the issue _before_ queueing it.
- **Reflection notes drift.** Without pruning, `CONDUIT.md` turns into the same _heap_ I was trying to escape. A compaction pass is non-negotiable.
- **Adapter surface is wide.** Pluggable sounds nice on a slide; in practice the tracker and runner contracts need a lot more sharpening before a third party could implement one without reading the source.
- **Local model latency.** Qwen2.5-Coder on my machine is fine for one task at a time, but the whole point of automation is _walking away_.

## ⏭️ What's Next

- A proper **playbook compactor** so `CONDUIT.md` stays useful past 50 entries.
- **Clarifying-question mode** before any task gets queued.
- A **second tracker adapter** – probably Linear or Jira – so the pluggability claim has actual proof.
- A **second runner adapter** – likely Claude Code – for the same reason.
- A **dashboard** showing which tasks are queued, in-flight, or stuck.
- Writing the **proper** launch post once I've got screenshots, real numbers, and at least one task-to-merge cycle that didn't need me at all.

## 💡 Final Thoughts

Conduit is my attempt to take everything that quietly broke about vibe coding and ADS – the chat amnesia, the code heaps, the maintenance debt – and push it into a structure that **survives the conversation ending**.

It's an implementation of Symphony, not a reinvention of it. The contribution, if there is one, is the **pluggability**: any tracker, any runner, one conductor that learns. The default of GitHub Issues + Aider + Qwen2.5-Coder is just where I happen to start.

If the bet pays off, my chat window goes quiet and my issue tracker gets noisy. That's the trade I want.

Disclaimer: this is a **placeholder** post and parts of it were drafted with AI assistance. Numbers, screenshots, and the link to the public repo will land once Conduit has earned them. 🤖🚇
