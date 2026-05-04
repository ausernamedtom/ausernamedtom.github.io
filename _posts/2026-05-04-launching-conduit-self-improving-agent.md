---
layout: post
title: "Launching Conduit – A Pluggable Take on OpenAI's Symphony Spec"
date: 2026-05-04
categories: [Agent Development, Vibe Coding]
tags: [conduit, symphony, aider, qwen, ai, agents, self-improving, github-issues]
---

> ⚠️ **Placeholder post** – an early write-up to mark the launch of **Conduit**. Expect rough edges and missing screenshots. I'll flesh it out once the dust settles.

## 🪦 Where Vibe Coding Took Me

I've spent the past year on vibe coding and what I started calling [_Agent Developed Software_](https://tomhofman.dev/posts/pang-the-table-tennis-simulator/) – first with [The Blue Car Game](https://tomhofman.dev/posts/vibe-coding-let-me-talk-you-through/), then [Baby Simulator](https://github.com/ausernamedtom/baby-simulator), then [Pang](https://github.com/ausernamedtom/pang). Each one taught me something. Each one also, eventually, ran aground.

The pattern was familiar:

- Early sessions felt magical – features shipped faster than I could review them.
- A few weeks in, the codebase had quietly turned into a less-than-ideal heap: duplicated helpers, contradictory abstractions, dead branches the agent forgot it left behind.
- Maintenance and pruning fell back to me, which meant I was back in the chat window doing exactly the work I'd hoped to offload.

Anything that lives only in a conversation evaporates the moment the conversation ends, and that's where a lot of the rot came from.

So my goal with Conduit is plain: **offset the chat to issues and tasks**.

## 🎼 Conduit is an Implementation of OpenAI's Symphony Spec

Conduit is an implementation of [**OpenAI's Symphony spec**](https://openai.com/) – the orchestration model that splits agent work into a **tracker** (what should be done), a **runner** (who does it), and a **conductor** (the loop that keeps them honest and learning).

The change I wanted to make: Symphony is a bit prescriptive about which tracker and runner you bring. Conduit is a **pluggable** implementation, with **free choice** of tracker and runner.

- **Tracker adapters** – GitHub Issues today; Jira, Linear, Trello, GitLab, plain Markdown TODOs tomorrow.
- **Runner adapters** – Aider + Qwen2.5-Coder is my default; Claude Code, Cursor's agent, OpenAI's Codex CLI are obvious alternatives.
- **A thin conductor** – Conduit hands a task from the tracker to the runner, watches the result, and writes a reflection note.

Tracker and runner choices tend to shift per project, client, or employer, and I'd rather not pick a fight with that reality.

## 🚇 The Default Stack

For the launch, the default wiring is:

- **Tracker:** [GitHub Issues](https://docs.github.com/en/issues) – already where my work lives.
- **Runner:** [Aider](https://aider.chat/) for git/diff/PR mechanics, driving [Qwen2.5-Coder](https://github.com/QwenLM/Qwen2.5-Coder) locally for code generation.
- **Conductor:** Conduit.

Aider handles the boring parts (repo maps, diffs, commits, conflicts) so I didn't have to rebuild them. Qwen2.5-Coder runs locally, which means I can iterate on Conduit without burning API credits while it misbehaves. None of those choices are load-bearing – swap the runner for Claude Code, swap the tracker for Linear, and Conduit shouldn't care.

## 🔁 The Self-Improving Loop

The loop is intentionally boring. No magic, no reinforcement learning – just a feedback file the runner reads before every task.

```text
   Tracker (Issues)  ──►  Conduit  ──►  Runner (Aider + Qwen2.5-Coder)
          ▲                                          │
          │                                          ▼
      New Tasks                              Pull Request / Patch
          │                                          │
          └────── Reflection Notes  ◄────  CI / Review Outcome
```

Each outcome becomes a short reflection note – things like _"when the issue mentions `_config.yml`, run `bundle exec jekyll build` before opening the PR"_. Notes live in a `CONDUIT.md` file the runner loads as context on the next task, so the lessons sit next to the code instead of inside a chat I closed last week.

## 🐛 Walking Through a Task

Imagine an issue like:

> **Title:** Sidebar avatar is blurry on retina displays
> **Body:** `assets/img/tom256.jpg` is only 256px. On 2x screens it looks fuzzy. Replace with a 512px version and update references if needed.
> **Labels:** `bug`, `good-first-issue`

Conduit picks it up, hands it to the runner with `CONDUIT.md` preloaded, and a PR opens. If CI passes and the PR gets approved, the outcome is logged and Conduit moves on. If CI fails, Conduit appends the failing log and asks the runner to try again, up to N retries, before tagging the issue `needs-human` and stepping away.

When I fix a `needs-human` issue manually, Conduit reads the diff and adds a reflection note describing what it would have done differently. That note shows up in the next run's context.

## 🚧 What's Not Working Yet

- **Issue scoping is fragile** – a vague issue produces a vague PR. I'd like a pre-flight step where Conduit asks clarifying questions before queueing.
- **Reflection notes drift** – without pruning, `CONDUIT.md` turns into the same heap I was trying to escape. A compaction pass is high on the list.
- **Adapter surface is wide** – the tracker and runner contracts need more sharpening before a third party could implement one comfortably.
- **Local model latency** – fine for one task at a time, but the whole point of automation is walking away.

## ⏭️ What's Next

- A **playbook compactor** so `CONDUIT.md` stays useful past 50 entries.
- **Clarifying-question mode** before tasks get queued.
- A second tracker adapter and a second runner adapter, so the pluggability story has actual proof.
- A small **dashboard** for queued / in-flight / stuck tasks.
- A proper launch post once there are screenshots, numbers, and at least one task-to-merge cycle that didn't need me.

Disclaimer: this is a **placeholder** post and parts of it were drafted with AI assistance. The link to the public repo will land once Conduit has earned it. 🤖🚇
