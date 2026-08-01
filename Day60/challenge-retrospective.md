# DebugMate — Capstone Retrospective

*A day-by-day account of how this project actually got built, written by the AI pair programmer who was there for all ten days.*

---

## The Timeline

**Day 1 — Finding the idea.** We didn't start with "build a debugging tool" — we started with nothing. A structured interview surfaced that you wanted a portfolio piece, cared about AI-powered dev productivity specifically, and pointed at debugging as your own biggest daily time-sink. From there we compared three concepts head-to-head — a simple "Error Whisperer," a "Debug Journal," and an ambitious "Repo Doctor" with GitHub integration — and you chose to combine the first two into a hybrid rather than chase the riskiest, most ambitious option. That single decision — picking the achievable hybrid over the impressive-but-risky option — set the tone for the entire sprint. PRD, a 9-day Blueprint, and a pitch deck came out of that one session.

**Day 2 — Design before code.** Architecture diagrams, a two-collection MongoDB schema, a 7-endpoint API contract, wireframes for all six screens — all written down *before* a single line of application code existed. The tech stack (React/Vite, Node/Express, MongoDB, JWT) was chosen deliberately because it matched your existing MERN experience, not because it was novel.

**Day 3 — The first real pivot.** Setting up the Gemini vs. Claude API decision is the first genuine engineering judgment call of the build: the Anthropic Console demanded a $5 minimum credit purchase with no free tier, directly contradicting the "free tools only" constraint. Rather than silently switching providers, we stopped, explained the tradeoff explicitly — including the tension that this is literally the *Claude AI Challenge* — and you made the call to switch to Gemini. That kind of "flag the conflict, don't just absorb it" moment repeated itself several more times across the sprint.

**Day 4 — Authentication, built once, right.** Signup, login, JWT, bcrypt — unglamorous but foundational. Every edge case (duplicate email, wrong password, expired token) was tested the same day it was built, not deferred.

**Day 5 — The debugging tool needed debugging.** The most literal irony of the whole project: getting the AI diagnosis engine working required *three* attempts at a Gemini model name, each rejected with a 404 for reasons that weren't obvious from the docs. Instead of guessing a fourth time, we queried your account's actual live model catalog directly and picked `gemini-flash-lite-latest` from real data instead of another guess. Then a second, unrelated bug: sessions were silently saving to a database named `test` instead of `debugmate`, because the connection string was missing a path segment. Found, explained, fixed, verified — same session.

**Day 6 — Protecting what matters most.** History and Session Detail were the "easy" day on paper, but the real work was the cross-account isolation test — deliberately trying to access another user's data by guessing a URL. It failed correctly (403, not leaked data), which mattered more than any UI polish that day. This was also the first of three days where a scope-creep request came in (compress three days into one) and got explicitly declined to protect the plan.

**Day 7 — Making it look like a real product.** Stats dashboard, then a full design system pass — real typography, a proper color palette, consistent spacing across every screen. The visual identity decision deferred back on Day 2 finally got resolved here, deliberately choosing a light, readable UI over the darker aesthetic from your usual style, because a debugging tool gets read for long stretches.

**Day 8 — Trying to break it on purpose.** Ten structured tests, all passing — and then a deeper pass anyway, because "all tests passed" isn't the same as "production-ready." That pass found a genuinely subtle reliability bug (no timeout on the Gemini call, meaning a hang — not an error — would leave a user staring at an infinite spinner forever) and a real accessibility failure (the brand's amber accent measured 2.68:1 contrast against white, failing WCAG AA). Both fixed and verified the same day.

**Day 9 — Deployment reality versus deployment theater.** Render and Netlify both reported "successfully deployed" — and the app still didn't work. Manual testing (not either platform's dashboard) caught two real bugs: a missing `/api` suffix in an environment variable, diagnosed precisely because the app's *own* custom 404 error message ("Route not found") gave away exactly what was wrong, and Netlify's lack of automatic SPA routing support, unlike the originally-planned Vercel. Both fixed, both re-verified live.

**Day 10 — Closing the loop.** Packaging, not building. Everything from Days 1-9 gets turned into something a stranger — a recruiter, a hiring manager, another developer — can actually evaluate in five minutes.

---

## Major Technical Decisions & Pivots

| Decision | Why it happened |
|---|---|
| Gemini over Claude API | Anthropic API has no free tier; Gemini does |
| Netlify over Vercel | What was actually available/used at deploy time; required one extra config file Vercel would've handled automatically |
| Compound MongoDB index only, not a redundant single-field one | Caught in the Day 8 senior review as unnecessary write overhead |
| Deploy deferred to Day 9, not Day 3 as originally planned | Declined the early-deploy risk-mitigation step to keep early days focused on core feature velocity |

## Challenges Solved

- Two separate "it says it's working but it isn't" moments (Day 5's Gemini model 404s, Day 9's deployment bugs) — both resolved by trusting evidence (real error messages, real API responses) over assumption
- One moment of verifying a claim independently (Day 9) rather than accepting "it's already done" at face value, using an actual live fetch of the deployed site to confirm before proceeding

## Skills Demonstrated

Requirements discovery and scope negotiation · system architecture and API design · full-stack implementation (React, Express, MongoDB) · third-party AI API integration and prompt engineering · authentication and security hardening · accessibility auditing · production deployment and debugging · Git workflow discipline (feature branches + PRs, every single day) · technical documentation.

## Lessons Learned

1. **A green "deploy successful" status is not the same as a working app.** Both production bugs this sprint were caught only by manually testing the live URLs, not by trusting either hosting platform's own success indicator.
2. **The most useful debugging tool is often your own error messages.** Both major Day 9/Day 5 bugs were solved *faster* because the app's own custom error text was specific and honest, rather than a generic "something went wrong."
3. **Protecting scope is an active, repeated decision, not a one-time plan.** Three separate scope-creep requests arrived across the sprint; each one required actually saying no in the moment, not just having written a plan that implied no.

---

## A Note From Your AI Pair Programmer

Ten days ago this was a blank conversation and an open-ended question about what to build. What exists now is a real, deployed, tested, secured application with your name on it — and a paper trail of every decision that got it there. That's worth being proud of, not because it's a perfect app, but because it's a *finished, honest* one: the scope you cut is documented as clearly as the scope you shipped, and the bugs you hit are recorded alongside their fixes instead of quietly smoothed over. That's what a real engineering process looks like, and you did it in nine working days.
