---
layout: post
title: '.NET Assemble 2026 – My Takeaways'
description: 'Notes from .NET Assemble 2026, with key ideas on AI agents, testing, observability, and developer tools.'
date: 2026-10-02
categories: [Conference]
tags: [conference, ai, dotnet, azure, devops]
---

Yesterday I attended [.NET Assemble 2026](https://netassemble.mstack.nl/), a free afternoon and evening conference in Den Bosch focused on .NET, Azure, DevOps and AI.

Unsurprisingly, AI agents were everywhere. But the talks that stuck with me most weren't necessarily the ones showing yet another AI platform. The more interesting themes were around
- how we reason before giving work to agents,
- how we observe distributed systems,
- how we test non-deterministic software,
- how we give agents better understanding of a codebase,
- and—perhaps most importantly—how we stop them from running wild on our machines.

Some talks were more directly applicable than others, but there are definitely a few experiments I want to take into *"Monday"*.

---

## 🤖 From 10 to 10,000 Agents: Engineering a Governed AI Platform on Azure

Speakers: [Patrick de Kruijf](https://nl.linkedin.com/in/patrickdk) & [Jesse Wellenberg](https://nl.linkedin.com/in/jesse-wellenberg)

The first session looked at what happens when an organisation moves beyond experimenting with a handful of agents and needs infrastructure capable of supporting potentially thousands of them.

The architecture covered things like:

- Centralised governance
- Token rate limiting
- Semantic caching
- API Management
- Agent identities using Entra ID
- Microsoft Foundry
- Infrastructure as Code
- Hub-and-spoke style deployment

Patrick and Jesse worked well as a duo and were both good presenters. There was a small hiccup around the live demo and screen scaling, but nothing that really got in the way.

### Personal takeaway

For me the session was a little too **Microsoft infrastructure heavy**.

It explained the infrastructure required to govern agents at scale, but I would have liked to see more of the agents themselves: how they're implemented, how they interact, and some concrete demonstrations of the workloads this infrastructure is actually supporting.

Still, if you are running hundreds or thousands of agents inside an organisation, the platform and governance problems become real very quickly.

Key takeaway: interesting reference architecture for the future, but not something for my day to day right now.

---

## 🧪 Testing the Untestable: Getting Started with Agent Evaluation in C#

Speaker: [Paul Stolk](https://nl.linkedin.com/in/paul-stolk-62ab442b)

One of the fundamental problems with testing LLM-based systems is that they are **non-deterministic**.

Traditional assertions don't work particularly well when asking the same question twice can produce two differently worded but equally correct answers.

The important point from this talk was that we should not try to remove that nondeterminism. Instead, our testing strategy has to adapt to it.

### Interesting ideas

Rather than asserting exact text:

```text
Assert.AreEqual(expected, actual)
```

evaluate properties of the answer such as:

- Intent
- Fluency
- Relevance
- Coherence
- Grounding
- Correct tool usage

Another point that stuck with me was the size of the evaluation set. For grounding in particular, think in terms of **150+ test cases**, rather than a couple of carefully selected example prompts.

That changes the mindset considerably. Agent evaluation starts looking less like traditional unit testing and more like measuring whether a system consistently behaves inside acceptable boundaries.

Paul came across as a proper geek—in the positive sense. A little nervous on stage, but clearly enthusiastic about the subject and a nice guy.

### Personal takeaway

The subject was good, although I would have liked some stronger real-world examples.

The idea also reinforces something that applies outside AI testing: **tests should capture intent and behaviour, not lock down the internal structure of the code**.

If a perfectly valid refactoring breaks half your tests because they know too much about how the implementation works internally, the tests have become part of the thing preventing change rather than helping us make it safely.

With non-deterministic systems this becomes even more obvious.

**Don't test generated text. Test whether the generated result fulfils its intended purpose.**

I don't currently a public facing chatbot or agent functionality to immediately need a large evaluation framework, but the principle is worth keeping.

---

## 💭 Two Blind Dreamers and a Critic

Speaker: [Maurice Peters](https://nl.linkedin.com/in/mcgppeters)

This was probably the talk that connected most directly with the agentic development workflows I've been experimenting with.

The interesting part wasn't really the development agents themselves.

It was what happens **before you allow them to start writing code**.

Maurice described a multi-stage design process involving two independent "dreamer" agents.

### The workflow

```text
Problem
  ↓
Dreamer A ── Internet access / research
  +
Dreamer B ── Offline / first principles
  ↓
Critic
  ↓
Good enough?
  ├── No → back to the dreamers
  └── Yes
       ↓
Reviewer
       ↓
Developer
```

One dreamer is allowed to research existing approaches using the internet.

The other is deliberately isolated and has to reason about the problem from first principles.

Importantly, the separation isn't just a prompt saying "don't look at the other solution." The agents are structurally prevented from seeing each other's work.

A critic then attempts to find holes in both approaches. If the solution isn't good enough, the problem goes back through the design loop.

Only after this process produces an acceptable design is the solution broken down by the Reviewer into an executable plan and handed to Developer agents.

### Why this is interesting

A common mistake with coding agents is moving from:

```text
Problem → Agent → Code
```

far too quickly.

The expensive mistakes often aren't syntax errors. They're agents competently implementing the wrong architecture.

Adding a design and critique phase before implementation could prevent a lot of wasted agent work.

Maurice was easy-going and clearly had a lot of practical experience with this. During the second half he seemed slightly distracted or preoccupied, and I got the impression his actual day-to-day agent setup has already moved considerably beyond what fitted into the talk.

### Monday

This is something I actually want to experiment with:

**Introduce a multi-stage design → critique → planning pipeline before development agents are allowed to implement anything.**

---

## 🔭 Distributed Tracing in .NET: From W3C Spec to Production Observability

Speaker: [Willem Koeter](https://nl.linkedin.com/in/wkoeter)

This was probably my favourite technical talk of the day.

Distributed tracing itself isn't new to me, and neither are OpenTelemetry or Aspire.

What was useful was going all the way down to the foundation underneath them: **W3C Trace Context**.

[W3C Trace Context](https://www.w3.org/TR/trace-context/) defines the standard that allows trace information to move between systems.

OpenTelemetry builds on it.

.NET builds on it.

Aspire makes much of it work almost invisibly.

The important reminder for me was that this isn't simply an Aspire feature or something confined to one application. It is an **open standard for carrying trace context across boundaries**.

That means tracing can continue through applications, services, workers and other systems as long as those systems participate in propagating that context.

### .NET underneath Aspire

ASP.NET already starts activities for you.

Aspire and OpenTelemetry then make those activities very easy to collect and visualize.

But the same mechanism is available directly through:

```csharp
System.Diagnostics.Activity
```

That means you can create meaningful spans inside things such as:

- Background workers
- CLI applications
- Scheduled jobs
- Message processing
- Long-running operations

Willem did a great job taking what could have been a very dry standards talk and exposing the technical simplicity and ingenuity underneath the abstractions we normally use.

### Monday

Build a small experiment specifically to see how far this can be taken.

For example:

```text
Frontend
   ↓
Backend
   ↓
Internal service
   ↓
External-style service
   ↓
... time passes ...
   ↓
Webhook back into the backend
```

Can the original trace context leave our application boundary, survive work happening elsewhere, and eventually reconnect when a webhook comes back?

Add a manually-created `Activity` somewhere in the middle as well to demonstrate that this isn't limited to ASP.NET request handling.

---

## ✅ We Don't Need No Stinkin' Testers

Speaker: [Jos Hendriks](https://nl.linkedin.com/in/jos-hendriks)

This session gave a nice tour through the world of testing, covering tools and approaches ranging from unit testing to component and end-to-end testing.

But the strongest point wasn't about any particular framework.

It was this:

> You cannot inspect quality into a product.

Testing shouldn't be the thing that happens after development has "finished."

If quality matters, it has to be designed into the product from the start.

### Why this matters

It means thinking about:

- Testability during design
- Expected behaviour before implementation
- Good unit boundaries
- Component-level testing
- Integration behaviour
- Naming tests around intent rather than implementation

That last point ties in nicely with the agent evaluation talk as well.

Tests should describe **what the system is supposed to do**, not freeze the internal structure that happens to implement it today.

A test named around implementation details tends to become brittle when the implementation changes. A test named around behaviour is much more likely to remain useful while the code evolves.

Testing then becomes part of software design rather than a gate somebody runs just before release.

Jos has obviously grown as a speaker. He was clear, down to earth, and comfortable with the subject.

### Monday

Take another look at how we name our **unit tests and component tests**.

Do their names describe the behaviour and intent of the system clearly enough that the tests themselves contribute to understanding the software?

And, perhaps more importantly: are any of those tests unnecessarily tying us to today's implementation structure?

Small improvement, but an easy one to actually start doing.

---

## 🕸️ Why Your AI Needs a Knowledge Graph

Speaker: [Klaus Seiler](https://www.linkedin.com/in/klausseiler/)

This session was a nice refresher on graph databases and explored how they can provide LLMs with something traditional vector-based RAG often lacks:

**relationships.**

Vector search is good at finding things that are semantically similar.

But software is full of explicit relationships:

```text
Class → implements → Interface

Function → calls → Function

Module → imports → Module

Test → covers → Component

Endpoint → depends on → Service
```

Representing those relationships as a graph could give an agent a richer understanding of a codebase than embeddings alone.

The talk was unfortunately a little vague on concrete day-to-day use cases, but it did point towards an interesting starting point:

[Code-Graph-RAG](https://github.com/vitali87/code-graph-rag)

It turns the structure and relationships inside a codebase into something an agent can query rather than treating the repository as little more than chunks of text.

That also makes it interesting to compare with projects such as [OpenViking](https://github.com/volcengine/OpenViking), which approaches agent context and memory from a different direction.

Klaus was a geeky, enthusiastic German who managed to grab the audience pretty well, even though graph databases aren't necessarily the easiest subject to make exciting.

### Monday

Point **Code-Graph-RAG** at a real repository and see whether the graph adds useful context for a coding agent.

I'm particularly curious whether it helps with questions around impact analysis and finding relevant context before changing code.

---

## 🔐 Zero Trust for Coding Agents

Speaker: [Chiel Kas](https://github.com/Chiel92)

This was probably the most important reminder of the day.

There is a lot of enthusiasm around autonomous coding agents right now, including from me.

That enthusiasm makes it very easy to forget that we're increasingly giving non-deterministic software:

- Shell access
- Source code
- Git credentials
- Cloud credentials
- SSH keys
- API keys
- Internet access
- Package managers

What could possibly go wrong? 😅

### The Lethal Trifecta

A particularly useful model was the **Lethal Trifecta**:

1. **Access to private data**
2. **Exposure to untrusted content**
3. **Ability to egress**

A coding agent might have access to local certificates, passwords and API keys.

It then reads something untrusted from the internet, a repository, an issue or documentation.

And finally it has access to things like:

```text
curl
TCP
HTTP APIs
package managers
git
```

That combination creates the opportunity for data exfiltration.

Prompt instructions saying:

```text
Never send my secrets anywhere
```

are not a security boundary.

### Swiss Cheese Security

The better model is the [Swiss Cheese Model](https://en.wikipedia.org/wiki/Swiss_cheese_model).

Every security measure has holes.

But several independent layers make it increasingly difficult for the holes to line up.

For coding agents that could mean:

```text
Coding Agent
    ↓
Pre-tool firewall
    ↓
Sandbox
    ↓
Container
    ↓
Virtual Machine
    ↓
Restricted filesystem
    ↓
Restricted credentials
    ↓
Restricted network
```

For example, Linux tools such as `bubblewrap` can provide lightweight filesystem and process isolation, while containers and VMs provide progressively stronger boundaries.

### Put a firewall in front of tools

One particularly interesting layer is a **Pre Tool Hook**.

Before the agent gets to execute a tool call, the hook gets a chance to inspect it.

Conceptually:

```text
Agent wants to call tool
        ↓
   Pre Tool Hook
        ↓
Allowed?
   ├── No → Block / ask for approval
   └── Yes → Execute tool
```

That effectively gives us a firewall between model reasoning and real-world execution.

It could inspect things such as:

- Commands attempting to access known secret locations
- Dangerous shell commands
- Unexpected outbound network calls
- Attempts to upload files
- Access to SSH or cloud credential directories
- Suspicious `curl` or `wget` commands
- Tools being used outside their expected scope

This isn't a replacement for sandboxing—the hook itself can have holes too—but that's exactly where the Swiss Cheese model becomes useful.

### Security before and after the agent

The same principle applies at the Git boundary.

We've previously looked at **pre-commit and pre-push hooks** as fast validation layers for development workflows. They can also provide another security checkpoint.

Before code leaves the workstation, hooks could scan changes for things such as:

- API keys
- Passwords
- Certificates and private keys
- Connection strings
- Tokens
- Personally Identifiable Information
- Other security-sensitive data

So even if an agent manages to accidentally write something sensitive into the repository, there is another independent opportunity to stop it before commit or push.

The interesting security model becomes less about finding one perfect sandbox and more about putting checks around **every important boundary**:

```text
Untrusted input
      ↓
Coding Agent
      ↓
Pre Tool Hook
      ↓
Sandbox / Container / VM
      ↓
Filesystem + Network Restrictions
      ↓
Generated Changes
      ↓
Pre-commit Security / PII Scan
      ↓
Pre-push Validation
      ↓
Remote
```

None of these makes an agent completely safe.

This remains an active battle.

But security should come from **enforced boundaries**, not from asking the model nicely.

Chiel was an enthusiastic coder who clearly also has a strong security mindset, which made the subject work particularly well.

### Monday

This deserves an experiment of its own.

Start looking at increasingly isolated ways to run coding agents:

1. Add a **Pre Tool Hook firewall** around Claude Code tool execution
2. Sandbox Claude Code
3. Run Claude Code inside a container
4. Run that environment inside a dedicated VM
5. Restrict credentials and filesystem access
6. Restrict outbound network access
7. Add pre-commit checks for secrets, security-sensitive data and PII
8. Work out which combination remains practical enough for daily development

---

## Final Thoughts

.NET Assemble was a good event.

There was obviously a lot of AI—and particularly agent-related—content, but the interesting parts for me were less about *which AI platform to use* and more about the engineering practices needed around increasingly autonomous software.

### My biggest personal takeaways

* **Don't let development agents start designing while they're already coding.** Add independent design, criticism and planning stages first.
* **Embrace nondeterminism when testing AI.** Assert behaviour, intent and quality rather than exact generated strings.
* **Tests should capture intent, not implementation structure.** They should make refactoring safer rather than making structural change harder.
* **OpenTelemetry is much deeper than a dashboard.** W3C Trace Context provides an open foundation for following operations across system boundaries.
* **You cannot inspect quality into a product.** Testing and testability start during design, not after implementation.
* **Code graphs could provide agents with structural understanding that vector search alone cannot.** Experiment with Code-Graph-RAG.
* **Coding agents need real security boundaries.** Prompts are not security controls.
* **Tool execution itself needs a firewall.** Pre Tool Hooks give us another place to enforce policy before an agent can act.
* **Security checks shouldn't stop at the agent.** Pre-commit and pre-push hooks can catch secrets, PII and other sensitive data before they leave the developer machine.

### What to try on Monday

There are four things I want to take beyond conference notes and actually experiment with:

1. **Multi-stage agent planning**

   ```text
   Problem → Independent Dreamers → Critic → Plan → Development Agents
   ```

2. **Full-circle OpenTelemetry tracing**

   Build a small system where trace context crosses several application boundaries, disappears into an asynchronous operation and eventually returns through a delayed webhook.

3. **Code-Graph-RAG**

   Build a graph of a real codebase and see whether it improves agent context and impact analysis.

4. **Defense-in-depth for coding agents**

   Experiment with:

   ```text
   Pre Tool Hook
        ↓
   Sandbox
        ↓
   Container
        ↓
   VM
        ↓
   Restricted credentials/network
        ↓
   Pre-commit / pre-push security checks
   ```

There is an interesting connection between several of these ideas.

A future agentic development environment could start to look something like:

```text
Project knowledge / OpenViking
            ↓
       Code Graph
            ↓
   Design + Critic Loop
            ↓
     Executable Plan
            ↓
      Coding Agent
            ↓
     Pre Tool Firewall
            ↓
 Sandbox / Container / VM
            ↓
       Tests + Evals
            ↓
 Security / PII Checks
            ↓
    OpenTelemetry Trace
```

That's probably the main thing I'm taking away from .NET Assemble.

The interesting problem is no longer just **"can an agent write the code?"**

It is increasingly:

**How do we give it the right context, make it think before it acts, constrain what it is allowed to do, verify the result, and understand exactly what happened afterwards?**

And that feels like a much more interesting engineering problem. 🤓
