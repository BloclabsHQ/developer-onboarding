# Product & Design Principles

## Feel-First Development
Before writing code, answer: "What does the user feel when they use this?" -- not what they do, but what they feel. The feeling drives the design. The design drives the engineering. Start with the emotional experience, work backward to code.

## Strip to Essence
If a screen has 12 buttons, you do not understand the problem yet. Strip everything to its core purpose. Test: can you explain what this screen does in one sentence? If not, simplify.

## Progressive Disclosure
Show only what is needed, when it is needed. Never dump everything on screen at once. Information hierarchy = visual hierarchy = interaction hierarchy. They must align.

## The 10x Rule of Polish
The last 10% of polish takes 90% of the effort -- and it is the 10% users remember. Ship the 90% fast, then invest real time in the 10% that creates quality: smooth animations, responsive feedback, consistent spacing, edge case handling.

## Invisible Design
The best interfaces feel like they have no design at all. The content IS the interface. Use system defaults -- system fonts, system colors, system spacing. Every custom component has a learning tax, a maintenance tax, an accessibility tax, and an edge-case tax. If a system component does 80% of what you need, use it.

## Animation as Communication
Animations are not decoration. Every animation must answer: "What is this teaching the user about what just happened?" Motion conveys meaning:
- Slide from right = went deeper
- Slide from bottom = temporary/modal
- Fade = state change
- Spring = physical interaction

## Accessibility is Not Optional
- Every interactive element must be reachable and operable without a mouse
- Every image needs descriptive alt text
- Color must never be the only means of conveying information
- Minimum touch target: 44x44 points
- Respect user preferences: reduced motion, high contrast, larger text

## App Launch is Your First Impression
Users decide in under 2 seconds. Show a placeholder immediately. Load data asynchronously. Never block the main thread during startup. The loading state IS a feature.
