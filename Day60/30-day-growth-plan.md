# DebugMate — 30-Day Growth Plan

A realistic roadmap from the current v1.0.0 MVP toward a genuinely more complete product, one achievable milestone per day. Each day assumes the same ~3-4 hour budget used throughout the original 10-day sprint, and builds directly on your existing stack (React/Vite, Node/Express, MongoDB Atlas, Gemini API, Render, Netlify) — no new stack, no rewrites.

---

## Week 1 — Foundations Real Users Need (Days 1-7)

| Day | Milestone |
|---|---|
| 1 | Set up automated backend tests: Jest + Supertest, cover `/api/auth/signup` and `/api/auth/login` happy paths + the edge cases already manually tested on Day 4/8 |
| 2 | Extend automated tests to `/api/diagnose`, `/api/sessions`, `/api/stats` (mock the Gemini call so tests don't burn real API quota) |
| 3 | Add a GitHub Actions workflow: run the test suite on every push, block merges on failure |
| 4 | Build password reset: `POST /api/auth/forgot-password` (generate + email a reset token) — this is the single most-cited gap from `future-scope.md` |
| 5 | Build the reset confirmation flow: `POST /api/auth/reset-password/:token` + frontend form |
| 6 | Separate dev/prod databases in MongoDB Atlas (currently shared per `ENVIRONMENT.md`'s noted limitation) — create a second cluster or database, update Render's `MONGODB_URI` |
| 7 | Add basic structured logging (e.g., `pino` or `winston`) to the backend, replacing raw `console.log`/`console.error` — makes Render's logs actually searchable |

## Week 2 — Deepen the Core Loop (Days 8-14)

| Day | Milestone |
|---|---|
| 8 | Add snippet export: a "Copy as Markdown" button on `DiagnosisResult`, formatting root cause/fix/concept as a shareable Markdown block |
| 9 | Add free-text search to History: a search box filtering sessions by error message/errorType client-side (dataset is small enough not to need a new backend endpoint yet) |
| 10 | Add manual tagging: extend `DebugSession` schema with a `tags: [String]` field, add a simple tag input on Session Detail |
| 11 | Add tag filtering to the History list, building on Day 10's schema change |
| 12 | Add pagination to `GET /api/sessions` (currently returns everything at once — fine at low volume, a real limitation at scale) |
| 13 | Add an "edit and re-diagnose" button: let a user tweak their error/code and resubmit without retyping from scratch |
| 14 | Polish pass: apply the Day 7 design system to all Week 2 additions so they don't look bolted-on |

## Week 3 — The Repo Doctor Feature (Days 15-21)

*This is the single highest-value item flagged in `future-scope.md` — the concept explicitly deferred back on Day 1 of the original sprint.*

| Day | Milestone |
|---|---|
| 15 | Design the feature: what does "paste a GitHub URL" actually fetch? Pick a scope (single file by path, not a full repo crawl) to stay achievable |
| 16 | Register a GitHub OAuth App; add `GITHUB_CLIENT_ID`/`GITHUB_CLIENT_SECRET` to your env setup, following the same pattern as `ENVIRONMENT.md` |
| 17 | Build `GET /api/github/file` — server-side endpoint that fetches a single file's content via the GitHub API given a repo + path |
| 18 | Extend the Gemini prompt (`geminiService.js`) to accept optional file context, keeping the existing labeled-text output format unchanged |
| 19 | Build the frontend: an optional "Link a GitHub file" input on the Diagnose form |
| 20 | Test thoroughly: public repo, private repo (requires OAuth token), file-not-found, rate-limited GitHub API — mirror the rigor from the original Day 8 testing pass |
| 21 | Update `docs/API.md`, `docs/SCHEMA.md`, and `docs/ARCHITECTURE.md` to reflect the new integration, same documentation discipline as the original sprint |

## Week 4 — Reach & Relaunch (Days 22-30)

| Day | Milestone |
|---|---|
| 22 | Add Google/GitHub social login (`passport.js` or a lightweight OAuth library) alongside, not replacing, existing email/password auth |
| 23 | Scaffold a VS Code extension shell that calls your existing, unchanged `POST /api/diagnose` endpoint |
| 24 | Build the extension's UI: a command that grabs the active editor's selection + the last error from VS Code's Problems panel |
| 25 | Test the extension against your live Render backend, handle auth (a personal access token stored in VS Code settings, simplest MVP approach) |
| 26 | Review Render/Netlify free-tier limits against actual usage — decide if it's time to upgrade anything, or if free tier still holds |
| 27 | Add a custom domain (if desired) to the Netlify frontend — cosmetic but meaningfully more "real product" for a portfolio link |
| 28 | Full regression pass across every feature built this month, same rigor as the original Day 8 |
| 29 | Update `README.md`, retake screenshots/demo video reflecting the new features, following the same pattern as Day 10 of the original sprint |
| 30 | Tag and release **v2.0.0** on GitHub, write release notes summarizing the month — mirroring how v1.0.0 was released on Day 10 |

---

*Use `daily-build-prompt.md` each day of this plan — same prompt every time, just change the day number.*
