---
layout: post
title: "Launching Conduit – A Self-Improving Agent Built on Aider and Qwen2.5-Coder"
date: 2026-05-04
categories: [Agent Development, Vibe Coding]
tags: [conduit, aider, qwen, ai, agents, self-improving, github-issues]
---

> ⚠️ **Placeholder post** – this is an early write-up to mark the launch of **Conduit**. Expect rough edges, missing screenshots, and TODOs scattered throughout. I'll flesh it out once the dust settles.

After spending a year mucking about with vibe coding and _Agent Developed Software_ on projects like [Pang](https://github.com/ausernamedtom/pang) and [Baby Simulator](https://github.com/ausernamedtom/baby-simulator), I kept running into the same wall: the agent could write code, but it couldn't really **own a backlog**. Every session started from zero. Every bug fix needed me to babysit the prompt.

So I built **Conduit** – a small orchestrator whose only job is to read GitHub Issues, hand them to a coding agent, and learn from what comes back.

## 🚇 What is Conduit?

Conduit is, as the name suggests, a pipe. On one end you have **GitHub Issues** – the ground truth for what should be done. On the other end you have a coding agent – in this case [**Aider**](https://aider.chat/) driving [**Qwen2.5-Coder**](https://github.com/QwenLM/Qwen2.5-Coder) locally.

In between, Conduit does three things:

1. **Triages** – picks the next issue based on labels, age, and an internal priority score.
2. **Executes** – spins up Aider in a fresh worktree, hands it the issue body, and lets it open a PR.
3. **Reflects** – reads the outcome (CI result, review comments, whether the PR merged) and writes that signal back into its own playbook.

That third step is the one I care about most. It's what makes Conduit **self-improving** rather than just _another_ agent runner.

## 🔁 The Self-Improving Loop

The loop is intentionally boring. No magic. No reinforcement learning rigs. Just a feedback file the agent reads before every run.

```text
GitHub Issues  ──►  Conduit Triage  ──►  Aider + Qwen2.5-Coder
       ▲                                          │
       │                                          ▼
   New Issues                              Pull Request
       │                                          │
       └────── Reflection Notes  ◄────  CI / Review Outcome
```

Each PR outcome turns into a short reflection note. Things like:

- _"When the issue mentions `_config.yml`, run `bundle exec jekyll build` before opening the PR."_
- _"Issues labeled `flaky-test` should never be closed without re-running the suite three times."_
- _"Don't touch `_posts/*.md` front matter unless the issue explicitly says so."_

These notes get appended to a `CONDUIT.md` file in the repo, which Aider loads as part of its context on the next run. Over time the playbook fills up with hard-won lessons, and the agent stops repeating the same mistakes.

## 🧩 Why Aider + Qwen2.5-Coder?

A few honest reasons:

- **Aider already does the boring parts well.** Git integration, repo maps, diff application, conflict handling. I didn't want to rebuild any of that.
- **Qwen2.5-Coder runs locally.** I can iterate on Conduit without burning API credits every time the loop misbehaves – and it misbehaves a lot in the early days.
- **It's good enough at small, well-scoped issues.** Which is exactly what GitHub Issues should be anyway. If an issue is too big for Qwen2.5-Coder, that's a signal the issue needs splitting, not that the model needs upgrading.

The split feels right: Aider is the _hands_, Qwen2.5-Coder is the _brain_, and Conduit is the _project manager_ that makes sure the right hands get the right brain on the right task.

## 🐛 Walking Through a Real Issue

Here's the kind of loop I'm running. Imagine an issue like:

> **Title:** Sidebar avatar is blurry on retina displays
> **Body:** `assets/img/tom256.jpg` is only 256px. On 2x screens it looks fuzzy. Replace with a 512px version and update references if needed.
> **Labels:** `bug`, `good-first-issue`

What Conduit does:

1. **Triage** picks it up because `good-first-issue` has a high priority score and the body is short and concrete.
2. **Aider** is launched against a fresh worktree with the issue body as the initial prompt and `CONDUIT.md` preloaded.
3. **Qwen2.5-Coder** suggests the change, Aider applies the diff, runs the build, and pushes a branch.
4. A PR opens. CI runs. If it passes and gets approved, Conduit logs `outcome: merged` and moves on.
5. If CI fails, Conduit reads the failure, asks Aider to try again _with the failing log appended_, and gives it up to N retries before tagging the issue `needs-human` and stepping away.

The important detail: every "needs-human" tag is a learning opportunity. When I fix it manually, Conduit watches the diff I write and adds a reflection note describing what it would have done differently. That note becomes part of the next run's context.

## 🚧 What's Not Working Yet

This is where I'm being deliberately unflattering, because I'd rather over-share early than oversell.

- **Issue scoping is fragile.** If a human writes a vague issue, Conduit happily produces a vague PR. I'm experimenting with a pre-flight step where Conduit asks clarifying questions on the issue itself before queueing it.
- **Reflection notes drift.** Without pruning, `CONDUIT.md` becomes a wall of contradictory advice. I need a compaction pass – probably another agent whose entire job is curating the playbook.
- **Local model latency.** Qwen2.5-Coder on my machine is fast enough for one issue at a time, but the whole point of automation is _walking away_. I'll likely move to a beefier host or a hosted endpoint before this is genuinely useful overnight.
- **No guardrails on destructive changes.** Right now Conduit could, in theory, rewrite history if Aider got creative. There's a hard allowlist of safe operations coming next.

## ⏭️ What's Next

Short list, in roughly the order I plan to tackle them:

- A proper **playbook compactor** so `CONDUIT.md` stays useful past 50 entries.
- **Clarifying-question mode** before any issue gets queued.
- A **dashboard** – even a tiny one – showing which issues are queued, in-flight, or stuck.
- Trying Conduit on a repo I _don't_ own, to see how it behaves without my mental model backing it up.
- Writing the **proper** launch post once I've got screenshots, real numbers, and at least one issue-to-merge cycle that didn't need me at all.

## 💡 Final Thoughts

Conduit isn't trying to be a general-purpose agent. It's trying to be the _smallest useful thing_ that turns a GitHub Issues backlog into merged PRs, while quietly getting better at it each week. Aider and Qwen2.5-Coder do the heavy lifting; Conduit just keeps the loop honest.

If you want to follow along, the repo will go public soon. Until then, this placeholder will have to do.

Disclaimer: this is a **placeholder** post and parts of it were drafted with AI assistance. Numbers, screenshots, and the link to the public repo will land once Conduit has earned them. 🤖🚇
