---
layout: post
title: "Pang The Table Tennis Simulator - Attempt #3 At Vibe Coding"
date: 2025-04-11
categories: [Game Development, Vibe Coding]
image:
  path: /assets/img/posts/2025-04-11-pang-the-table-tennis-simulator/pang-github-issues.webp
  alt: GitHub Issues created by the agent before solving problems in code
---

After experimenting with vibe coding in my first two projects, [The Blue Car Game](https://tomhofman.dev/posts/vibe-coding-let-me-talk-you-through/) and [Baby Simulator](https://github.com/ausernamedtom/baby-simulator), I decided to take on a new challenge: creating a table tennis simulator called **Pang**. This project marks my third attempt at vibe coding, where the goal is to build something fun and creative in a short amount of time.

## 🌈 What is Vibe Coding?

Vibe coding is all about creating software with minimal planning, focusing on creativity and experimentation. My first two attempts followed this approach, where I simply prompted ideas like:

> Create a top-down 2D driving simulator with a blue hue.

Or:

> Create a baby simulator where you pick up the baby, feed it, and soothe it to sleep.

For this project, I’m introducing a new term: _Agent Developed Software_ (ADS). Unlike vibe coding, ADS involves the agent taking full control of the development process, from writing specs to coding.

## 🐛 Pitfalls of the First Two Attempts

While my first two attempts at vibe coding weren’t terrible, they had significant challenges. Adding new features often broke existing ones, and the resulting code turned into a messy slurry of JavaScript crammed into a single file. This made debugging and extending the code increasingly difficult.

It reminded me of early personal projects—fun but chaotic, like a hobbyist or junior developer’s work. The lack of structure and foresight led to:

- Coding without considering future changes.
- Difficulty understanding or maintaining the codebase.

These issues highlighted the need for better planning and structure in future projects.

## 🎓 _ADS: Agent Developed Software_

To avoid repeating the mistakes of my earlier attempts, I focused on improving the development process for Pang. Here’s what I did differently:

- **Set boundaries**: Defined a clear [Tech Stack](https://github.com/ausernamedtom/pang/blob/main/docs/technical-stack.md).
- **Establish rules**: Created [Development Rules](https://github.com/ausernamedtom/pang/blob/main/docs/development-rules.md) to guide the process.
- **Write specifications**: Used Gherkin-style [server.feature](https://github.com/ausernamedtom/pang/blob/main/specs/server.feature) specs to define features clearly.
- **Test rigorously**: Ensured every change was tested to prevent breaking existing features.

By setting up this documentation I aimed to create a more maintainable and scalable project for the agent to work. The goal is to maintain a repository that could be contributed to by human developers or even completely taken over by after the initial phase.

## ⏭️ What’s Next?

While the current version of [Pang](https://github.com/ausernamedtom/pang) isn’t playable yet, I’m excited to continue refining it. My next steps include:

- Better rules to aid Test Driven Development 🧪
- An actual playable first version 🏓
- Gryo control in native applications for an advanced match of Pang with risk of flying mobile phones. 🙋‍♂️

I’ll also evaluate the effectiveness of the ADS approach and refine it further for future projects.

## 💡 Final Thoughts

Attempting ~~vibe coding~~ _Agent Developed Software_ for the ~~third~~ _first_ time has been an exciting and rewarding experience. While Pang is far from perfect, it represents a significant step forward in my journey as an Agent Developed Software Engineer. I’m eager to see how this project evolves and what I’ll learn along the way.

Stay tuned for updates, and feel free to share your thoughts or suggestions in the comments below!

Disclaimer: I used AI to rewrite parts of this blog but added some personal touches here and there 🤖🤓
