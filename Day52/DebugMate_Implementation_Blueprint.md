# DebugMate — Implementation Blueprint (Days 2–10)

**Program:** AB Talks 60-Day Claude AI Challenge — 10-Day Capstone
**Author:** Rishi Kumar Kar
**Status:** Single source of truth for Days 2–10. Generated on Day 1 after requirements were finalized (see companion PRD). **Updated end of Day 2** with the finalized tech stack and design decisions below — Days 3–10 now assume these as fixed.

## Finalized Tech Stack (confirmed Day 2 — see ARCHITECTURE.md for full rationale)

- **Frontend:** React (Vite)
- **Backend:** Node.js + Express
- **Database:** MongoDB Atlas (free M0 cluster) via Mongoose
- **Auth:** JWT (`jsonwebtoken`) + `bcryptjs`
- **AI:** Claude API (Anthropic), server-side only
- **Hosting:** Vercel (frontend) · Render (backend) · MongoDB Atlas (database) — all free tier
- **Other libraries:** `cors`, `dotenv`, `react-router-dom`, `axios`, `date-fns`

Full architecture diagrams, DB schema, API contract, and UI wireframes now live in `docs/ARCHITECTURE.md`, `docs/SCHEMA.md`, `docs/API.md`, and `docs/UI-WIREFRAMES.md` — Days 4 onward should treat those as authoritative alongside this Blueprint.

---

## How to use this document

Each day below is self-contained. **Paste the entire section for that day (plus the "Project Context" block below) into a fresh AI conversation** to continue building exactly where the previous day left off — no re-explaining, no re-planning. Update the "Progress Log" mini-section at the bottom of each day once it's done, so the next day's AI session has an accurate handoff.

Time budget assumed: **3–4 hours/day**. Every day is scoped to fit that budget with some slack for debugging surprises.

---

## Project Context (paste this into every new day's conversation)

> **Project:** DebugMate — an AI-powered, multi-user web app where a developer pastes an error message + code, and the AI returns a root-cause diagnosis, a fix, and a plain-English explanation of the underlying concept. Every session is auto-saved to the user's personal history (tagged by language & error type), and a stats dashboard shows patterns over time (common error types, languages, sessions over time).
>
> **v1.0 scope:** Email/password auth · core diagnosis engine (any language, AI auto-detects) · session history/journal · stats dashboard · polished, mobile-responsive UI with empty/loading/error states · deployed to a live public URL.
>
> **Explicitly out of scope for v1.0:** GitHub repo integration, VS Code/browser extensions, advanced search/filter, snippet export, social login, team features, monetization, password reset/email verification.
>
> **Constraints:** No paid tools/services. Every manual step outside the AI chat must be explained click-by-click, with confirmation (screenshot) required before moving on. Time budget ~3-4 hrs/day.
>
> Full detail lives in the PRD and this Blueprint. **Do not redesign or re-plan — follow the day's plan below.**

---

# Day 2 — Design (Architecture, Tech Stack & UI Blueprint)

### 🎯 Objective
Turn the PRD into a concrete technical plan: choose the tech stack, design the database schema, define the API contract, design the AI prompt strategy, and sketch the UI — all *before* writing implementation code.

### 📖 What I'll learn
How to make deliberate architecture decisions instead of defaulting to habit; how to design a database schema around features (not the other way around); how to design a prompt for structured, reliable AI output.

### 🛠 Features to build
None yet — this is a planning/design day. Output is documents/diagrams, not code (a few tiny throwaway scripts to sanity-check an idea are fine).

### 📝 Step-by-step implementation plan
1. **Tech stack decision — FINALIZED:** React (Vite) frontend + Node.js/Express backend + MongoDB Atlas (Mongoose) database + JWT/bcryptjs auth + Claude API diagnosis engine + Vercel (frontend hosting) + Render (backend hosting). See `docs/ARCHITECTURE.md` for full rationale. This matches your existing MERN experience and keeps the whole stack in JavaScript, minimizing context-switching in a tight timeline.
2. **System architecture sketch.** Draw (even in a text diagram) three layers: Client (React SPA) → API server (Express REST routes) → Database (MongoDB) + external AI API call from server only (never from the client, to protect the API key).
3. **Database schema design.** Two collections minimum:
   - `users`: `_id`, `email`, `passwordHash`, `createdAt`.
   - `sessions` (debugging sessions — name it `debugSessions` to avoid confusion with auth sessions): `_id`, `userId` (ref to users), `errorMessage`, `codeSnippet`, `language` (AI-detected), `errorType` (AI-detected short label), `rootCause`, `fix`, `conceptExplanation`, `createdAt`.
4. **API contract design.** Define routes on paper before coding:
   - `POST /api/auth/signup` — body `{email, password}` → `{token, user}`
   - `POST /api/auth/login` — body `{email, password}` → `{token, user}`
   - `POST /api/diagnose` (auth required) — body `{errorMessage, codeSnippet}` → `{rootCause, fix, conceptExplanation, language, errorType}` (also persists to DB)
   - `GET /api/sessions` (auth required) → list of past sessions (summary fields only)
   - `GET /api/sessions/:id` (auth required) → full session detail
   - `GET /api/stats` (auth required) → `{byErrorType: [...], byLanguage: [...], overTime: [...]}`
5. **AI prompt strategy design.** Per your established preference, design the Claude API prompt to return **labeled plain-text output**, not JSON — e.g.:
   ```
   LANGUAGE: <detected language>
   ERROR_TYPE: <short label>
   ROOT_CAUSE: <2-3 sentences>
   FIX: <code or steps>
   CONCEPT: <plain-English explanation>
   ```
   Then parse with simple string splitting on these labels server-side. Write out the exact system prompt text today so Day 5 just implements it.
6. **UI wireframes (low-fidelity, can be boxes-and-labels on paper or a text description).** Screens needed: Login/Signup, Main Diagnose screen (form + result panel), History list, Session detail, Stats dashboard, shared Nav/Layout with empty states for History and Stats when a user has 0 sessions.
7. **Visual identity decision.** Confirm your usual dark glassmorphism aesthetic (dark background, frosted glass panels, amber/teal/orange accents, Space Grotesk + JetBrains Mono) as DebugMate's branding, or choose a fresh direction if you want DebugMate to look distinct from past projects. Write the decision down — Day 7 (polish) depends on it.
8. **Write it all down** in a `docs/design.md` file (see Files section) — this is what Day 3 onward will build from.

### 📂 Files and folders to create or modify
- `docs/design.md` — architecture diagram (text), DB schema, API contract, AI prompt text, wireframe descriptions, visual identity decision.
- No app code yet.

### 🔗 APIs, libraries, services, or tools to integrate
- None installed yet — just decide today. (Claude API key acquisition can happen today if you want it ready for Day 5: sign up at console.anthropic.com, generate an API key, store it somewhere safe — **do not commit it to git**.)

### 🧪 Testing tasks
- "Test" the API contract by walking through each user story from the PRD and confirming every step has a corresponding route.
- Sanity-check the DB schema against the Stats Dashboard requirement — can `byErrorType`, `byLanguage`, and `overTime` actually be computed from the fields you defined? (Yes, via `errorType`, `language`, and `createdAt`.)

### 🐞 Common issues and debugging tips
- Don't over-design: if a decision doesn't affect Day 3+ work, defer it (e.g., exact shade of amber can wait for Day 7).
- If undecided between two stacks, default to what you already know (MERN) — a familiar stack de-risks the whole timeline far more than a "better" unfamiliar one.

### ✅ End-of-day checklist
- [ ] Tech stack decision written down explicitly
- [ ] Architecture diagram exists (even as text)
- [ ] DB schema for `users` and `debugSessions` defined
- [ ] All 6 API routes defined with request/response shapes
- [ ] AI prompt text drafted with labeled plain-text output format
- [ ] Wireframes/screen list exists for all 6 screens
- [ ] Visual identity decision recorded
- [ ] Claude API key generated and saved securely (optional today, needed by Day 5)

### 📸 Expected project state and screenshots to capture
- No running app yet. Screenshot: `docs/design.md` open, showing the schema and API contract sections.

### ➡️ Handoff notes for Day 3
> Day 2 complete. Tech stack: **[fill in your final decision here]**. DB schema, API contract, and AI prompt text are finalized in `docs/design.md`. Day 3 will scaffold the actual project structure and get a deployable "hello world" skeleton live.

---

# Day 3 — Setup (Project Scaffolding & Deployable Skeleton)

### 🎯 Objective
Go from zero code to a running local dev environment **and** a minimal version already deployed live — so hosting problems surface now, not on Day 9.

### 📖 What I'll learn
Project scaffolding conventions for your chosen stack; environment variable management; the value of deploying early and often ("deploy a blank page before you deploy a real app").

### 🛠 Features to build
- Empty but running frontend (shows a placeholder "DebugMate — coming soon" page)
- Empty but running backend (one health-check route, e.g. `GET /api/health` → `{status: "ok"}`)
- Both connected and both deployed live

### 📝 Step-by-step implementation plan
1. Initialize a git repository; create `.gitignore` (node_modules, `.env`, build folders).
2. Scaffold backend: initialize Node project, install Express, create a minimal server with the `/api/health` route.
3. Scaffold frontend: initialize React app (e.g., via Vite for fast setup), replace boilerplate with a simple placeholder page.
4. Connect frontend to backend locally (confirm the placeholder page can successfully call `/api/health` and display the result — proves the wiring works before any real feature exists).
5. Set up MongoDB: create a free-tier cloud database (e.g., MongoDB Atlas free tier), get a connection string, store it in a local `.env` file (never committed).
6. Push repo to GitHub (I'll walk you through creating the repo and pushing, step by step, with screenshot confirmation at each stage).
7. Deploy backend to a free-tier host and frontend to a free-tier static host; connect environment variables (DB connection string, JWT secret, Claude API key placeholder) in the hosting dashboard, not in code.
8. Confirm the **deployed** frontend can call the **deployed** backend's health check successfully. This is today's real finish line.

### 📂 Files and folders to create or modify
```
debugmate/
├── backend/
│   ├── server.js (or src/index.js)
│   ├── routes/health.js
│   ├── .env (local only, gitignored)
│   ├── .env.example (committed, no real secrets)
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   └── main.jsx
│   └── package.json
├── docs/
│   └── design.md (from Day 2)
├── .gitignore
└── README.md (placeholder for now)
```

### 🔗 APIs, libraries, services, or tools to integrate
- Express (backend routing)
- MongoDB Atlas (free-tier managed database) + Mongoose (ODM) for connecting
- Vite + React (frontend scaffold)
- GitHub (version control + source for deployment)
- **Render** (backend hosting, free Web Service tier, auto-deploy from GitHub)
- **Vercel** (frontend hosting, free Hobby tier, auto-deploy from GitHub)

### 🧪 Testing tasks
- Local: hit `http://localhost:<port>/api/health` directly in a browser and confirm the JSON response.
- Local: confirm the frontend placeholder page displays that response (proves the fetch/CORS setup works).
- Production: repeat both checks against the live deployed URLs.

### 🐞 Common issues and debugging tips
- **CORS errors** when frontend calls backend locally: enable `cors()` middleware in Express during development.
- **Environment variables not loading:** confirm `.env` is in the backend root and loaded via `dotenv` before any code that reads `process.env`.
- **Deployed frontend can't reach deployed backend:** double check you're calling the *production* backend URL, not `localhost`, in the deployed frontend build — this is usually handled via an environment variable read at build time.
- **MongoDB Atlas connection refused:** check the Atlas Network Access tab allows connections from your hosting provider (often "allow access from anywhere" is the simplest fix for a free-tier student project).

### ✅ End-of-day checklist
- [ ] Git repo initialized and pushed to GitHub
- [ ] Backend runs locally and returns health check
- [ ] Frontend runs locally and displays health check result
- [ ] MongoDB Atlas cluster created, connection string in local `.env`
- [ ] Backend deployed live, health check reachable via public URL
- [ ] Frontend deployed live, successfully calls deployed backend
- [ ] `.env.example` committed with placeholder keys (no real secrets)

### 📸 Expected project state and screenshots to capture
- Screenshot of local placeholder page showing "status: ok"
- Screenshot of the **live deployed** URL showing the same
- Screenshot of the GitHub repo with initial commit history

### ➡️ Handoff notes for Day 4
> Day 3 complete. Live URLs: frontend = `[fill in]`, backend = `[fill in]`. Local dev environment works end-to-end. Day 4 builds real authentication (signup/login) on top of this skeleton — the health-check route and deploy pipeline should not need to change, just extend.

---

# Day 4 — Implementation: Authentication

### 🎯 Objective
Build real, working multi-user authentication: signup, login, protected routes — the foundation every other feature depends on.

### 📖 What I'll learn
Password hashing and why it matters; token-based session auth (JWT); protecting API routes; protecting frontend routes.

### 🛠 Features to build
- Signup form (email + password) → creates a user
- Login form → returns a session token
- Logged-in state persists across page reloads
- Logout
- Protected routes: diagnosis/history/stats pages inaccessible when logged out

### 📝 Step-by-step implementation plan
1. Backend: create the `User` model (Mongoose schema matching Day 2's design: email, passwordHash, createdAt).
2. Backend: implement `POST /api/auth/signup` — validate input, hash password (bcrypt), save user, return a signed JWT + basic user info (never the password hash).
3. Backend: implement `POST /api/auth/login` — find user by email, compare password hash, return JWT on success or a clear 401 error on failure.
4. Backend: create an `authMiddleware` that reads the JWT from the request, verifies it, and attaches the user to the request — used to protect all routes built from Day 5 onward.
5. Frontend: build Signup and Login pages/forms with basic client-side validation (non-empty fields, valid email format).
6. Frontend: on successful login/signup, store the token (e.g., in memory + a secure storage mechanism decided on Day 2/3) and redirect to the main app.
7. Frontend: create a simple auth context/hook so any component can check "is the user logged in" and get their info.
8. Frontend: create a route guard — redirect to Login if an unauthenticated user tries to reach a protected page.
9. Frontend: add a visible Logout action that clears the token and redirects to Login.
10. Wire up basic error display: wrong password, duplicate email on signup, network failure — each shows a clear message, not a console-only error.

### 📂 Files and folders to create or modify
```
backend/
├── models/User.js
├── routes/auth.js
├── middleware/authMiddleware.js
└── utils/hash.js (optional helper)

frontend/src/
├── pages/Login.jsx
├── pages/Signup.jsx
├── context/AuthContext.jsx
├── components/ProtectedRoute.jsx
└── api/authApi.js
```

### 🔗 APIs, libraries, services, or tools to integrate
- `bcrypt` (or `bcryptjs`) for password hashing
- `jsonwebtoken` for signing/verifying tokens
- React Router (if not already set up) for protected route handling

### 🧪 Testing tasks
- Sign up with a new email → confirm user appears in MongoDB Atlas (via the Atlas UI).
- Sign up with an already-used email → confirm a clear error, not a crash.
- Log in with correct credentials → confirm redirect to main app.
- Log in with wrong password → confirm clear error message.
- Reload the page while logged in → confirm still logged in.
- Try to directly visit a protected URL while logged out → confirm redirect to Login.
- Log out → confirm protected pages become inaccessible again.

### 🐞 Common issues and debugging tips
- **Token not persisting across reloads:** confirm you're reading it back from storage on app load, not just keeping it in a React state variable that resets on refresh.
- **401 errors on every request even when logged in:** check the Authorization header format matches exactly what the backend middleware expects (commonly `Bearer <token>`).
- **Password hash mismatch:** make sure you're comparing with `bcrypt.compare`, never comparing hashes with `===`.
- **CORS blocking the Authorization header:** some CORS configs need to explicitly allow custom headers.

### ✅ End-of-day checklist
- [ ] User model matches Day 2 schema
- [ ] Signup works and creates a hashed-password user in the DB
- [ ] Login works and returns a valid token
- [ ] Auth middleware correctly blocks requests without a valid token
- [ ] Frontend persists login across reloads
- [ ] Protected routes redirect unauthenticated users
- [ ] Logout works
- [ ] All testing tasks above pass locally

### 📸 Expected project state and screenshots to capture
- Screenshot of Signup form and successful redirect
- Screenshot of MongoDB Atlas showing the new user document (password field visibly hashed, not plain text)
- Screenshot of a protected page redirecting to Login when logged out

### ➡️ Handoff notes for Day 5
> Day 4 complete. Auth is fully working locally (deploy is redone on Day 9, not each day). `authMiddleware` is ready to protect any new route. Day 5 builds the core diagnosis feature as a protected route using this same middleware — no auth code should need to change, just be reused.

---

# Day 5 — Implementation: Core Diagnosis Engine

### 🎯 Objective
Build the heart of the product: paste error + code → get an AI-generated diagnosis, fix, and concept explanation.

### 📖 What I'll learn
Calling the Claude API from a backend server; prompt design for structured, parseable output; handling AI latency and failure gracefully in a UI.

### 🛠 Features to build
- Diagnose form (error message + code snippet inputs)
- Backend endpoint that calls the Claude API with the Day 2 prompt design
- Parsing the labeled plain-text response into structured fields
- Result displayed clearly in the UI (root cause / fix / concept, separated visually)
- Loading and error states for the AI call
- Result automatically saved to the database (sets up Day 6)

### 📝 Step-by-step implementation plan
1. Backend: create `POST /api/diagnose` behind `authMiddleware`.
2. Backend: implement the Claude API call using the exact system prompt drafted in `docs/design.md` on Day 2, requesting the labeled plain-text format (`LANGUAGE:`, `ERROR_TYPE:`, `ROOT_CAUSE:`, `FIX:`, `CONCEPT:`).
3. Backend: write a small parser that splits the AI's raw text response on those labels into a structured object — no JSON parsing of AI output, per your established preference; simple string matching is more robust here.
4. Backend: add retry logic with exponential backoff for transient API failures (2-3 retries max), and a clear error response if all retries fail.
5. Backend: on a successful parse, save a new `debugSessions` document (userId, input fields, and all parsed output fields, createdAt) — this makes Day 6 nearly free.
6. Backend: return the parsed result to the frontend.
7. Frontend: build the Diagnose page — a form with an error-message textarea and a code textarea, a Submit button, and a results panel below.
8. Frontend: show a loading indicator while waiting for the AI response (this call may take a few seconds).
9. Frontend: render the result with clear visual separation between Root Cause, Fix, and Concept Explanation (e.g., three distinct cards/sections).
10. Frontend: handle and display errors (AI failure, network failure, empty input) without breaking the page.

### 📂 Files and folders to create or modify
```
backend/
├── routes/diagnose.js
├── services/claudeService.js   (API call + retry logic)
├── utils/parseDiagnosis.js     (labeled-text parser)
└── models/DebugSession.js

frontend/src/
├── pages/Diagnose.jsx
├── components/DiagnosisResult.jsx
├── components/LoadingState.jsx
└── api/diagnoseApi.js
```

### 🔗 APIs, libraries, services, or tools to integrate
- Anthropic Claude API (model: whichever current model your account has access to — check available models rather than hardcoding an old one) called **server-side only** (API key lives in backend `.env`, never sent to the frontend)
- Existing `authMiddleware` from Day 4 to protect the route

### 🧪 Testing tasks
- Submit a real, simple error (e.g., a Python `IndexError` with a short snippet) → confirm sensible, correctly-parsed output.
- Submit in a second language (e.g., JavaScript `TypeError`) → confirm language auto-detection works and output still parses correctly.
- Submit empty fields → confirm client-side validation blocks submission with a clear message.
- Temporarily break the API key (e.g., typo it) → confirm the retry logic runs and the user sees a friendly error, not a crash.
- Confirm each successful diagnosis creates a new document in `debugSessions` in MongoDB Atlas.

### 🐞 Common issues and debugging tips
- **Parser breaks on slightly different AI phrasing:** make the prompt very explicit about the exact label format and test with 3-4 different real errors to confirm consistency before moving on.
- **Long response times feel broken:** always show a loading state the moment the request is sent, not just a static spinner icon with no context — a short "Diagnosing your error..." message reassures the user.
- **API key exposed in frontend:** double-check the Claude API call only ever happens in backend code, never in a frontend `fetch`.
- **Rate limits during testing:** space out repeated manual tests if you hit them.

### ✅ End-of-day checklist
- [ ] `/api/diagnose` works end-to-end locally, protected by auth
- [ ] Response reliably parses into the 5 labeled fields across multiple test errors
- [ ] Retry/backoff logic confirmed working on a simulated failure
- [ ] Successful diagnosis is saved to `debugSessions` in the DB
- [ ] Frontend shows loading, success, and error states correctly
- [ ] Tested with at least 2 different languages

### 📸 Expected project state and screenshots to capture
- Screenshot of a submitted error + the returned diagnosis (all 3 sections visible)
- Screenshot of the loading state
- Screenshot of MongoDB Atlas showing a saved `debugSessions` document

### ➡️ Handoff notes for Day 6
> Day 5 complete. The diagnosis engine works locally and every result is already being saved to `debugSessions`. Day 6 does **not** need to build the save logic — it already exists. Day 6 focuses purely on *reading* that data back: a history list view and a session detail view.

---

# Day 6 — Implementation: Session History / Journal

### 🎯 Objective
Let users browse and revisit their past debugging sessions — turning the raw saved data from Day 5 into a usable personal journal.

### 📖 What I'll learn
Building list + detail views backed by real user-scoped data; designing meaningful empty states; basic pagination/sorting concepts.

### 🛠 Features to build
- History list page: all of the current user's past sessions, newest first
- Each list item shows language, error-type tag, and date at a glance
- Clicking a session opens a detail view with the full original diagnosis
- Empty state for users with zero sessions

### 📝 Step-by-step implementation plan
1. Backend: implement `GET /api/sessions` (protected) — query `debugSessions` filtered by the logged-in user's ID, sorted newest first, returning only summary fields (id, language, errorType, createdAt) for a lightweight list.
2. Backend: implement `GET /api/sessions/:id` (protected) — return the full session document, but only if it belongs to the requesting user (critical: verify ownership, don't just trust the ID in the URL).
3. Frontend: build the History page — fetch and render the list, each item as a clickable card/row showing language + error type + relative date (e.g., "2 hours ago").
4. Frontend: design and implement the empty state — friendly message + a call-to-action button linking to the Diagnose page ("You haven't debugged anything yet — try it now").
5. Frontend: build the Session Detail page/view — same visual layout as the Diagnose result panel from Day 5 (root cause / fix / concept), reused as a shared component for consistency.
6. Frontend: add a loading state for the list fetch and a "not found / not yours" state for an invalid or foreign session ID.
7. Frontend: link from the Diagnose page's result panel to "View in history" after a save, closing the loop between Day 5 and Day 6.

### 📂 Files and folders to create or modify
```
backend/
└── routes/sessions.js   (GET /api/sessions, GET /api/sessions/:id)

frontend/src/
├── pages/History.jsx
├── pages/SessionDetail.jsx
├── components/SessionCard.jsx
├── components/EmptyState.jsx   (reusable for History and later Stats)
└── api/sessionsApi.js
```

### 🔗 APIs, libraries, services, or tools to integrate
- No new external services — this day is pure application logic on top of Day 4 (auth) and Day 5 (data).
- Optional: a small date-formatting helper (e.g., `date-fns`) for "2 hours ago"-style timestamps.

### 🧪 Testing tasks
- As a user with several sessions, confirm the list shows all of them, correctly sorted newest-first.
- Click into a session and confirm the detail view matches what was originally shown on Day 5.
- Log in as a second test user and confirm they see an empty state, not the first user's sessions (critical security test).
- Manually try to access another user's session by guessing/editing an ID in the URL → confirm it's blocked, not just hidden in the UI.
- Confirm the empty state renders correctly for a brand-new account.

### 🐞 Common issues and debugging tips
- **Data leaking across users:** this is the most important thing to test today — always filter by `userId` from the authenticated token, never from a client-supplied value.
- **List and detail views drifting out of sync visually:** reuse the same result-display component from Day 5 rather than rebuilding it, so they stay consistent automatically.
- **Empty state showing briefly then flashing to content (or vice versa):** make sure the loading state resolves before deciding whether to show the empty state.

### ✅ End-of-day checklist
- [ ] History list shows only the logged-in user's sessions, correctly sorted
- [ ] Session detail view matches the original diagnosis
- [ ] Empty state implemented and tested with a fresh account
- [ ] Cross-user access blocked and tested with two accounts
- [ ] Diagnose page links to the new history entry after saving

### 📸 Expected project state and screenshots to capture
- Screenshot of History list with multiple sessions
- Screenshot of the empty state
- Screenshot of a Session Detail view

### ➡️ Handoff notes for Day 7
> Day 6 complete. Users can now fully browse their history. All the raw data needed for stats (language, errorType, createdAt per session) already exists in `debugSessions`. Day 7 adds an aggregated stats view on top of this same data, plus a full UI/branding polish pass across every screen built so far (Days 4-6).

---

# Day 7 — Implementation: Stats Dashboard + Polish Pass

### 🎯 Objective
Add the stats dashboard (the feature that turns DebugMate into a visible growth-tracker), and spend real time making every existing screen feel like a finished product.

### 📖 What I'll learn
Writing simple aggregation queries; turning raw data into a small, honest dashboard; the discipline of a dedicated polish pass instead of "polishing as you go."

### 🛠 Features to build
- Stats dashboard: sessions by error type, sessions by language, sessions over time
- Full visual polish pass: consistent branding, spacing, empty/loading/error states, mobile responsiveness — across Login, Signup, Diagnose, History, Session Detail, and the new Stats page

### 📝 Step-by-step implementation plan
1. Backend: implement `GET /api/stats` (protected) — aggregate the current user's `debugSessions`:
   - Group by `errorType`, count each.
   - Group by `language`, count each.
   - Group by day (or week, if session count is low), count sessions per period.
   (Use MongoDB's aggregation pipeline, or — if simpler given the timeline — fetch the user's sessions and aggregate in plain JavaScript on the server. Either is fine for this data volume; don't over-engineer.)
2. Backend: handle the low-data case explicitly — if the user has 0-1 sessions, return a shape the frontend can render as a partial/empty state rather than an error.
3. Frontend: build the Stats page with three sections: error-type breakdown, language breakdown, sessions-over-time. Simple bar lists or small charts are enough — this doesn't need to be a data-viz showcase.
4. Frontend: implement the "not enough data yet" empty/partial state for new or light users.
5. **Polish pass — go screen by screen** (Login, Signup, Diagnose, History, Session Detail, Stats):
   - Confirm the visual identity decided on Day 2 (color palette, fonts, spacing) is applied consistently.
   - Confirm every async action (login, diagnose, load history, load stats) has a visible loading state.
   - Confirm every failure case (bad login, AI failure, network error) has a clear, non-technical error message.
   - Confirm every screen is usable at a narrow mobile width (~375px) — check for overflow, cramped buttons, unreadable text.
   - Add a simple shared navigation/header so moving between Diagnose / History / Stats / Logout feels like one cohesive app, not separate pages.
6. Fix anything that looks unfinished: inconsistent spacing, missing hover states, abrupt layout shifts.

### 📂 Files and folders to create or modify
```
backend/
└── routes/stats.js

frontend/src/
├── pages/Stats.jsx
├── components/StatBlock.jsx
├── components/NavBar.jsx        (shared layout, if not already built)
└── styles/ (global spacing, color, typography cleanup — exact location depends on your Day 2 stack choice)
```

### 🔗 APIs, libraries, services, or tools to integrate
- Optional lightweight charting (only if it doesn't cost significant setup time — simple styled bar lists are a perfectly good v1.0 alternative to a charting library).

### 🧪 Testing tasks
- Confirm stats numbers match reality (manually count sessions by type/language and compare to the dashboard).
- Test the dashboard with a fresh (empty-data) account and with a heavily-used test account.
- Resize the browser (or use device emulation) to confirm every screen is usable on mobile.
- Click through the entire app end-to-end once, start to finish, as if you were a brand-new user, and note anything that feels rough.

### 🐞 Common issues and debugging tips
- **Aggregation returning unexpected shapes:** log the raw aggregation output first, then build the frontend chart/list against the actual shape, not the assumed one.
- **Mobile layout breaking on the diagnose form's two textareas:** stack them vertically on narrow screens rather than trying to keep a side-by-side layout.
- **Polish pass taking too long:** timebox it — note anything more than cosmetic as a Day 10 or future-scope item rather than blocking today's finish line.

### ✅ End-of-day checklist
- [ ] Stats dashboard shows correct error-type, language, and time-based breakdowns
- [ ] Empty/partial state handled for low-data users
- [ ] Every screen reviewed for consistent branding and spacing
- [ ] Every async action has a loading state
- [ ] Every failure case has a friendly error message
- [ ] Full app tested at mobile width
- [ ] Shared navigation in place across all screens

### 📸 Expected project state and screenshots to capture
- Screenshot of the Stats dashboard with real data
- Screenshot of the same dashboard's empty/partial state
- Screenshots of Diagnose, History, and Stats pages at mobile width

### ➡️ Handoff notes for Day 8
> Day 7 complete. All four core features (auth, diagnosis, history, stats) are functionally complete and visually polished locally. Day 8 shifts from building to breaking — systematic testing across functional flows, edge cases, and devices, with bugs logged and fixed as found.

---

# Day 8 — Testing

### 🎯 Objective
Deliberately try to break the app before a real user (or recruiter) does — find and fix bugs, edge cases, and rough edges while there's still time to fix them properly.

### 📖 What I'll learn
Structured manual testing (test plans, edge cases); the difference between "works on the happy path" and "actually reliable"; how to prioritize bug fixes under time pressure.

### 🛠 Features to build
None new — this day is testing and fixing existing features.

### 📝 Step-by-step implementation plan
1. Write a simple test checklist covering every user story from the PRD (Section 8) — one line per story, pass/fail.
2. **Functional testing** — walk through the full flow fresh: signup → login → diagnose (2-3 different errors/languages) → view history → view stats → logout → login again.
3. **Edge case testing:**
   - Extremely long code snippet (near/at any input limits)
   - Error message with no code, or code with no error message
   - Special characters, code with unusual formatting/indentation
   - Rapid repeated submissions (double-click the submit button)
   - Expired/invalid session token behavior
   - Wrong password multiple times in a row
4. **Cross-account isolation testing** (repeat from Day 6, now with fresh eyes): confirm no data leaks between two test accounts anywhere in the app, including stats.
5. **Mobile/responsive testing:** repeat the full flow at mobile width, not just visual spot-checks.
6. **Error-handling testing:** deliberately disconnect network mid-request, or temporarily invalidate the AI API key, and confirm the app fails gracefully everywhere, not just on the Diagnose page.
7. **Bug triage:** for every bug found, classify as Fix Now (breaks a core flow) vs Log for Day 10 (cosmetic, minor) vs Future Scope (out of budget) — protect the timeline by not chasing every minor issue today.
8. Fix all "Fix Now" bugs and re-test them.

### 📂 Files and folders to create or modify
```
docs/
└── test-plan.md   (checklist + results + bug list, created today)
```
(Plus whatever application files need bug fixes — this varies by what's found.)

### 🔗 APIs, libraries, services, or tools to integrate
- None new. Browser dev tools (device emulation, network throttling/offline mode) are your main testing tools today.

### 🧪 Testing tasks
(This entire day *is* the testing task — see implementation plan above.)

### 🐞 Common issues and debugging tips
- **Can't reproduce a bug:** check the browser console and backend logs together — most "mystery" bugs show an error in one of the two.
- **Too many minor issues found at once:** resist fixing everything today; triage ruthlessly using the Fix Now / Day 10 / Future Scope buckets above.
- **Auth token edge cases are the highest-value place to spend extra time** — they're both the most likely to have subtle bugs and the most damaging if broken (data leaking between users).

### ✅ End-of-day checklist
- [ ] Full test checklist completed against every PRD user story
- [ ] Edge cases tested and results logged
- [ ] Cross-account isolation re-confirmed
- [ ] Mobile flow tested end-to-end
- [ ] Error-handling tested under simulated failures
- [ ] All "Fix Now" bugs fixed and re-tested
- [ ] Remaining minor bugs logged for Day 10 or Future Scope

### 📸 Expected project state and screenshots to capture
- Screenshot of `docs/test-plan.md` with completed checklist
- Screenshot of any notable bug found and then fixed (before/after)

### ➡️ Handoff notes for Day 9
> Day 8 complete. Core flows are tested and stable locally, with known-good behavior on functional, edge-case, cross-account, mobile, and failure scenarios. Remaining bugs (if any) are logged in `docs/test-plan.md` as Day 10 items. Day 9 deploys this tested version to production.

---

# Day 9 — Deployment

### 🎯 Objective
Get the fully-featured, tested application live in production, with all environment configuration correct, and confirm it works there exactly as it did locally.

### 📖 What I'll learn
Production environment configuration; the difference between "works locally" and "works in production"; basic production smoke testing.

### 🛠 Features to build
None new — this day is deployment and production verification of everything built Days 4-8.

### 📝 Step-by-step implementation plan
1. Review the deployment skeleton from Day 3 — confirm it still auto-deploys from your GitHub repo (push → deploy), or redo the connection if it's drifted.
2. Set all required production environment variables in the hosting dashboard: MongoDB Atlas connection string, JWT secret, Claude API key. **Never commit these to git** — confirm `.env` is still gitignored.
3. Deploy the backend; confirm the `/api/health` route still responds in production.
4. Deploy the frontend; confirm it's pointing at the **production** backend URL, not localhost.
5. Run the full Day 8 functional test checklist again, this time against the **live production URLs**, not localhost.
6. Specifically re-test: signup, login, diagnose (at least 2 languages), history, stats, mobile view, logout — all against the live site.
7. Check production logs (via your hosting provider's dashboard) for any errors that don't show up locally (e.g., different Node version, missing environment variable).
8. If using a custom domain (optional, free options only) — set it up now; otherwise the default hosting URL is completely fine for a portfolio project.
9. Confirm the Claude API key works from the deployed environment specifically (some keys or quotas behave differently in production vs local testing).

### 📂 Files and folders to create or modify
- No new files typically — this is configuration in the hosting dashboards, plus any small fixes uncovered by production-only issues.
- Update `docs/design.md` or a new `docs/deployment.md` with the final live URLs and deployment steps taken, for your own reference and for the README on Day 10.

### 🔗 APIs, libraries, services, or tools to integrate
- Vercel (frontend) and Render (backend) — finalized Day 2, deployed skeleton Day 3
- MongoDB Atlas (already set up on Day 3, now confirmed with production access rules)

### 🧪 Testing tasks
- Full smoke test of every core flow against the live URL (see step 6 above).
- Confirm mobile responsiveness on an actual phone browser if possible, not just emulation.
- Confirm a brand new signup on production works end-to-end (don't just re-test with an existing local account carried over).

### 🐞 Common issues and debugging tips
- **"Works locally, fails in production":** almost always an environment variable that's missing or has a typo in the hosting dashboard — check that first.
- **CORS errors only in production:** the backend's CORS config may be allowing `localhost` but not your deployed frontend's actual domain — update it to include the production frontend URL.
- **Cold starts on free-tier hosting:** some free tiers sleep after inactivity, causing a slow first request — this is expected behavior for a free-tier student project and worth a one-line note in your README, not a bug to chase.

### ✅ End-of-day checklist
- [ ] Backend deployed and health check passes in production
- [ ] Frontend deployed and correctly calls production backend
- [ ] All environment variables set correctly in hosting dashboard
- [ ] Full functional test checklist re-passed against live URLs
- [ ] New signup tested fresh in production
- [ ] Mobile view confirmed on production
- [ ] Live URLs documented in `docs/deployment.md`

### 📸 Expected project state and screenshots to capture
- Screenshot of the live production URL in a real browser address bar, showing the app working
- Screenshot of a successful production signup + diagnosis
- Screenshot of hosting dashboard showing successful deploy status

### ➡️ Handoff notes for Day 10
> Day 9 complete. DebugMate is live at: **[fill in production URL]**. All core flows verified in production. Day 10 is polish, documentation, and packaging for portfolio use — no new features, only final fixes, README, demo video, and submission.

---

# Day 10 — Maintenance, Polish & Launch

### 🎯 Objective
Turn the deployed, working app into a complete, presentable portfolio piece: fix any remaining known issues, write documentation, capture a demo, and formally close out the capstone.

### 📖 What I'll learn
How to package a project for recruiters (README structure, demo capture); the habit of a final QA pass before calling something "done"; writing a short retrospective — a valuable interview-story habit.

### 🛠 Features to build
None new. Only: bug fixes logged from Day 8/9, documentation, and demo assets.

### 📝 Step-by-step implementation plan
1. Review the bug list from Day 8 ("Day 10" bucket) and fix anything still outstanding; re-test each fix in production (redeploy as needed).
2. Do one final full click-through of the live app, end to end, on both desktop and mobile, as a completely fresh user — this is your last chance to catch anything.
3. Write the real `README.md`: project name and one-line pitch, problem it solves, key features, tech stack, screenshots, live demo link, how to run locally, and a short "what I'd build next" section (pulling directly from the PRD's Future Scope list).
4. Take clean, well-cropped screenshots of: Login/Signup, Diagnose flow with a real result, History list, Session Detail, and Stats dashboard — for the README and for your portfolio/LinkedIn.
5. Record a short demo video or GIF (60-90 seconds is plenty): show signup → diagnose a real error → view it in history → view stats. This is one of the highest-leverage assets for a job application.
6. Write a short retrospective (3-5 sentences): what you built, what you learned, what you'd do differently — genuinely useful both for your own growth and as interview material.
7. Final commit and push; confirm the deployed version matches the final GitHub `main` branch exactly.
8. Submit the capstone per the AB Talks challenge's submission process.
9. Optional but recommended: write a short LinkedIn post using your existing content style (per your ABTalks 60 Days Claude Challenge series), showcasing DebugMate with the demo GIF/video.

### 📂 Files and folders to create or modify
```
README.md              (rewritten, complete)
docs/
├── design.md           (from Day 2, left as historical reference)
├── deployment.md        (from Day 9)
├── test-plan.md         (from Day 8)
└── retrospective.md     (new, today)
assets/
└── screenshots/, demo.gif (or link to hosted video)
```

### 🔗 APIs, libraries, services, or tools to integrate
- None new. A free screen-recording tool of your choice for the demo GIF/video (no purchase required — standard OS screen recording is enough).

### 🧪 Testing tasks
- Final full regression pass of every core flow, in production, one more time.
- Confirm every link in the README (live demo, GitHub repo) actually works when clicked fresh.

### 🐞 Common issues and debugging tips
- **Scope creep on "just one more feature":** resist it — Day 10 is about finishing and presenting, not building. Anything new goes in Future Scope, not into the app today.
- **Demo video too long or unfocused:** script it in one sentence first ("show signup, one real bug fixed, history, stats") and record 2-3 takes rather than one long unplanned one.

### ✅ End-of-day checklist
- [ ] All outstanding Day 8/9 bugs fixed and re-verified in production
- [ ] Full fresh end-to-end click-through passed, desktop and mobile
- [ ] README complete with screenshots, live link, setup instructions, and future scope
- [ ] Demo video/GIF recorded and linked/embedded
- [ ] Retrospective written
- [ ] Final commit pushed; GitHub `main` matches production
- [ ] Capstone submitted

### 📸 Expected project state and screenshots to capture
- Final polished screenshots of every core screen (for README/portfolio)
- The demo GIF/video itself
- Screenshot of the submitted capstone confirmation (if applicable)

### ➡️ Handoff notes (capstone complete)
> DebugMate v1.0 is live, tested, documented, and submitted. Future Scope (GitHub repo integration, extensions, advanced search, social login, team features, monetization) is documented in the PRD and README for anyone — including a future you — who wants to keep building.

---

## Master Progress Log

*(Update this section at the end of each day — paste the updated version into the next day's fresh AI conversation along with the Project Context block.)*

| Day | Status | Key decisions / notes | Live/local state |
|---|---|---|---|
| 1 | ✅ Complete | Project: DebugMate. Requirements finalized (see PRD). | N/A |
| 2 | ⬜ Not started | | |
| 3 | ⬜ Not started | | |
| 4 | ⬜ Not started | | |
| 5 | ⬜ Not started | | |
| 6 | ⬜ Not started | | |
| 7 | ⬜ Not started | | |
| 8 | ⬜ Not started | | |
| 9 | ⬜ Not started | | |
| 10 | ⬜ Not started | | |
