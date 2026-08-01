# DebugMate — Daily Build Prompt (Reusable, Days 1-30)

Copy this into a fresh AI conversation each day of the 30-Day Growth Plan. Only the day number changes.

---

```
Day [X] of the DebugMate 30-Day Growth Plan.

Read these files from the repository (https://github.com/R07777/debugmate) before doing anything:
- docs/DebugMate_PRD.docx
- docs/ARCHITECTURE.md
- docs/SCHEMA.md
- docs/API.md
- docs/30-day-growth-plan.md
- docs/challenge-retrospective.md (for context on how this project was originally built)

These are the source of truth. Do not redesign the existing v1.0.0 application or contradict decisions already documented in ARCHITECTURE.md unless you find a critical issue — if you do, explain why and ask before changing anything.

Find "Day [X]" in docs/30-day-growth-plan.md and treat that single milestone as today's ENTIRE scope. Do not start tomorrow's milestone. Do not expand today's milestone into something bigger than what's written.

Standing rules:
- Assume I need guidance for every manual step outside this chat (installing packages, configuring services, deploying, etc.) — explain it with exact button names, menu names, and commands.
- Wait for my confirmation and a screenshot before continuing to the next step.
- Never assume I've completed a step.
- Prefer free tools and services only, matching everything already used in this project (MongoDB Atlas free tier, Gemini API free tier, Render/Netlify free tiers).
- Keep the existing design system (client/src/index.css) and coding patterns (thin routes, logic in services/utils, ownership checks on every user-scoped query) — match the existing codebase's style, don't introduce a new one.

Today's process:
1. Briefly explain what we're building today and why, in one or two sentences.
2. Generate the complete contents of every file needed — no snippets, no "...existing code...", no placeholders.
3. State exactly where each file belongs and whether it's new or replaces an existing file.
4. Give me every command to run, one step at a time, pausing for my confirmation after each meaningful step.
5. Test today's milestone thoroughly before considering it done — mirror the rigor of the original project's Day 8 testing standard.
6. Update any documentation file that today's change affects (same discipline as the original 10-day build).
7. Help me commit and push today's work using the same feature-branch + PR workflow used throughout the original build.
8. End with a short summary: what got done today, and what tomorrow's milestone (Day [X+1]) will be, per the growth plan.

Do not implement anything beyond today's single milestone, even if it seems like an obvious next step. Staying scoped to one day at a time is the entire point of this plan.
```
