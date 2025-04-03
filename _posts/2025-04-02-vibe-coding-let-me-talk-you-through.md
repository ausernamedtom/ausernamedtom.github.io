---
layout: post
title: "Vibe Coding - Let me talk you through"
date: 2025-03-15
categories: [gamedev,devlog,vibe]
---

> Vibe coding (also vibecoding) is an AI-dependent programming technique where a person describes a problem in a few sentences as a prompt to a large language model (LLM) tuned for coding.

[Wikipedia: Vibe Coding](https://en.wikipedia.org/wiki/Vibe_coding)

Below you can see the conversation I had and the result can be found here [Blue City Driving Simulator](/projects/blue-city-driving-simulator/index.html)

> # Blue City Driving Simulator Development Log
> 
> ## Initial Request
> User: "create a top down 2d driving simulator with a blue hue"
> 
> ## Development Process
> 
> ### 1. Project Setup
> Created the basic project structure with:
> - index.html
> - styles.css
> - game.js
> - README.md
> 
> ### 2. Core Features Implemented
> - Top-down 2D driving mechanics
> - Blue-themed car with realistic physics
> - Three-lane road with lane markers
> - Keyboard controls (WASD or arrow keys)
> 
> ### 3. Additional Features Added
> 1. Gold Coins
>    - Random spawning every 2 seconds
>    - 3-second lifetime
>    - Collision detection with car
>    - Score system (10 points per coin)
> 
> 2. Visual Effects
>    - Fireworks on coin collection
>      - 20 particles per firework
>      - Gold to orange color scheme
>      - Gravity effect on particles
>    - Screen shake effect
>      - 200ms duration
>      - Random x/y offset
>      - Smooth return to normal
> 
> ### 4. Production Optimization
> Created a dist version with:
> - Minified JavaScript (game.min.js)
> - Minified CSS (styles.min.css)
> - Production-ready HTML
> - Custom SVG favicon
> 
> ## File Structure
> ```
> .
> ├── dist/
> │   ├── index.html
> │   ├── styles.min.css
> │   ├── game.min.js
> │   └── favicon.svg
> ├── index.html
> ├── styles.css
> ├── game.js
> ├── README.md
> └── favicon.svg
> ```
> 
> ## How to Run
> 1. Navigate to the dist directory
> 2. Run a local server (e.g., `python3 -m http.server 8000`)
> 3. Open `http://localhost:8000` in a web browser
> 
> ## Controls
> - W or Up Arrow: Accelerate forward
> - S or Down Arrow: Brake/Reverse
> - A or Left Arrow: Turn left
> - D or Right Arrow: Turn right
> 
> ## Features
> - Smooth car physics with acceleration and friction
> - Three-lane road with lane markers
> - Collectible gold coins
> - Firework effects on coin collection
> - Screen shake feedback
> - Score tracking
> - Blue-themed aesthetic
> - Production-optimized build 
