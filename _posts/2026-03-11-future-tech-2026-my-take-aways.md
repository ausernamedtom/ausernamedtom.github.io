---
layout: post
title: "Future Tech 2026 – My Takeaways"
date: 2026-03-11
categories: [Conference]
tags: [conference, ai, dotnet, aspire, robotics, cloud]
---

Today I attended **Future Tech 2026**, a day packed with talks ranging from robotics and programming languages to AI-driven development and cloud observability.

Overall it was a **solid event**. Some talks were stronger than others, but I think I managed to catch the most interesting ones. Two themes clearly stood out throughout the day: **Agentic Development** and **Aspire / cloud tooling**.

---

## 🤖 Building an Intelligent Robot Arm from Scratch

**Speaker:** [Iulia Feroli / Back to Engineering](https://www.youtube.com/@backtoengineering)

This talk was refreshing because it moved away from purely software topics and into **Physical AI / Robotics**.

Iulia shared how she started building her own robot arm after going through a _"tired of AI"_ phase. By working with hardware she rediscovered the **joy of engineering**, **experimentation** and **renewed fun in use of AI**.

### Highlights

- Software built using **C++ and MicroPython**
- Hardware included **Arduino boards, Pi Pico, and HuggingFace robot arms**
- Iterated from simple **SG90 servos with potentiometers** to advanced **encoder-based teleoperation with dual robot arms**
- Eventually connected the system to a **specialized LLM to help generate datasets**

One interesting takeaway was the **difference between software and hardware iteration**. When something goes wrong in robotics, you don't just rerun code, you can literally **burn through boards and servos**.

**Key takeaway:**
Physical AI is fascinating and fun, but experimentation can get expensive quickly.

Maybe I should start a _desktop companion (display)_ side project... 🤔🤓

---

## 💻 The Past, Present and Future of Programming Languages

**Speaker:** [Kevlin Henney](https://about.me/kevlin)

Kevlin took a step back and looked at the **evolution of programming languages**.

### Key observations

- Most of the **top programming languages today aren't actually new**
- Many core language ideas are **30+ years old**
- Only a handful of popular languages in the top 20 were created in the **21st century**

What _has_ changed is the **speed at which languages evolve**. Modern languages expand and iterate much faster than in the past.

**Main message:**
Languages themselves may not be revolutionary anymore, but **their evolution cycles are accelerating**.

---

## 🧪 Red → Green → WOW: TDD in 105 Minutes

**Speakers:** [Jacob Duijzer](https://github.com/jacobduijzer) & [Jos Hendriks](https://blog.netwatwezoeken.nl/)

This workshop focused on **Test Driven Development**.

The demonstration used **xUnit** and the classic **FizzBuzz example**, showing the familiar **red, green, refactor cycle** to iterate from _static_ `if i == 3` to `i % 3 == 0` logic.

### Key points

- **TDD** can be applied at **multiple levels of development**
- Tests help **capture intent**
- The **red > green > refactor** cycle encourages **small iterative improvements**

### Personal takeaway

**TDD** is still extremely valuable, and I should practice it more consistently.

---

## 🤖 Use AI to Build AI (Without Losing Your Mind)

**Speaker:** [Maddy Montaquila](https://github.com/maddymontaquila)

This keynote was easily one of the **most engaging talks of the day**.

Maddy, who works as **PM on Aspire** at Microsoft, showed how her team increasingly uses **AI to build software itself**.

### Core ideas

- **AI will smell fear**, let it go (a bit) 😉
- **Build for AI** in your workflow for better **collaboration with AI**

Her enthusiasm made the talk very accessible and she did a great job **selling the technology without overselling it**.

**Key takeaway:**
Developers should **optimize their workflow for continued AI collaboration**, not treat AI as optional or sparingly used tooling.

---

## ⚙️ Aspire - Beyond the Basics

**Speaker:** [Loek Duys](https://github.com/loekd)

This session focused on what happens **under the hood of Aspire**.

Instead of focusing on surface features, Loek explored the **internal components** that power the framework.

### Internal components discussed

- **DCP**
- **DCPCtrl**
- **DCPD**

These components manage orchestration and lifecycle management of services behind the scenes. It was also highlighted that it's `kubectl` compatible.

### Interesting part

The talk included CLI demonstrations showing the relationship between processes and how Aspire handles process lifecycle and dependencies.

Loek used a tool, he made, called **ScriptRunner** to automate the command-line interactions during the demo.

**Key takeaway:**
Aspire performs a lot of **heavy lifting when connecting distributed services**, but understanding its internal architecture helps when debugging or extending it. And look at **ScriptRunner** if you want to demonstrate CLI workflows.

---

## ☁️ Cloud App & Infrastructure Observability

**Speaker:** [Magnus Mårtensson](https://noopman.com/about)

This was one of the **least flashy but most practically useful talks** of the day.

Magnus highlighted a fundamental limitation in Azure monitoring:

> Azure currently only understands **resources**, not what makes them **applications**.

### Azure Preview Technology

Microsoft is introducing:

- **Service Groups** - [Microsoft Learn: What are Azure Service Groups?](https://learn.microsoft.com/en-us/azure/governance/service-groups/overview)
- **Health Models** - [Microsoft Learn: Health models in Azure Monitor (preview)](https://learn.microsoft.com/en-us/azure/azure-monitor/health-models/overview)

**Service Groups** add a **grouping layer** to combine **resources** that make logical **applications**. **Health Models** can be used on top of that to better understand the health of an **application**.

### Why this matters

Cloud deployments with large amounts of resources often contain many logical applications that depend on shared infrastructure and/or other resources. Without grouping, it becomes very difficult to understand the health of a logical application.

### Benefits

- Near-zero effort to start using Service Groups
- Minimal permissions required
- Improved operational visibility

**Personal takeaway:**
These are features I want to experiment as soon as possible, to prevent logic built on top of certain **tags** on resources, **resource group** and unnessary hierarchical **subscriptions** trees.

---

## 📜 Should You Start Using Spec-Driven Development?

**Speaker:** [Maarten van Diemen](https://github.com/maartenvandiemen)

This talk explored **Spec-Driven Development**, where features are built based on explicitly defined specifications.

### Tools discussed

- **SpecKit**

Initially it seemed like this might **replace BDD**, but after seeing the workflow it felt more like **during-development tooling** rather than a **commit to repository** documentation of specifications.

Other similar projects mentioned were:

- **OpenSpec**
- **AgentOS**

### Potential downside

**Spec management** can become complex. Specs are typically stored as Markdown files, which can easily grow into **dozens of documents per Spec** that become difficult to maintain.

**Takeaway:**
Interesting concept, but likely **complementary to BDD** rather than a replacement or taking much of it's place.

---

## 🤖 Practical Multi-Agent Architecture

**Speaker:** [Geert van der Cruijsen](https://github.com/Geertvdc)

This talk focused on a practical problem: building AI agents is easy, but **getting multiple agents to cooperate effectively** is much harder.

The session showed how multi-agent systems can solve real business problems by **splitting responsibilities into small specialized agents**.

### Key ideas

- Keep **agent context small**
- Prefer **micro-agents with focused responsibilities**
- Smaller prompts and smaller contexts lead to more reliable behavior
- Agents should communicate through **clear handovers or workflow steps**

### Demo

The live demo used **n8n** to model a workflow for email processing:

1. A **classification agent** determines whether an email contains an invoice
2. If classified as an invoice, the workflow **hands over to another step**
3. The invoice is then **labeled or processed further**

This showed how AI agents can be integrated into **practical automation pipelines**.

### Pitfalls highlighted

One important warning:

> Don't use agents for problems that can already be solved with simple automation.

AI agents introduce complexity, so they should only be used when reasoning or classification is genuinely required.

**Key takeaway:**
Use tools like **n8n to quickly prototype agent workflows**, but continuously evaluate whether parts of the system should be replaced with **regular deterministic automation** once the workflow stabilizes.

---

## Final Thoughts

Future Tech 2026 was a **mixed bag of talks**, but the stronger sessions were definitely worth attending.

### My biggest personal takeaways

- Experiment with **Service Groups** and **Health Models**
- Spend more time exploring **Aspire**
- Practice **TDD more consistently**
- Continue adapting to **AI-assisted development**
