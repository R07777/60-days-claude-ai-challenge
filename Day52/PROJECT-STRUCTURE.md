# DebugMate — Project Structure

This is the complete folder layout for the repository, reflecting the confirmed tech stack (React/Vite client, Node/Express server). This structure is what Day 3 (Setup) will actually create — no changes should be needed on later implementation days, only new files inside these folders.

```
debugmate/
├── .gitignore
├── LICENSE
├── README.md                      # placeholder now, rewritten fully on Day 10
│
├── docs/                          # all planning & design documents (this folder)
│   ├── PRD.md                     # (or .docx, from Day 1)
│   ├── IMPLEMENTATION-BLUEPRINT.md
│   ├── ARCHITECTURE.md
│   ├── SCHEMA.md
│   ├── API.md
│   ├── UI-WIREFRAMES.md
│   ├── PROJECT-STRUCTURE.md       # this file
│   ├── design.md                  # Day 2 working notes / AI prompt text
│   ├── test-plan.md               # created Day 8
│   ├── deployment.md              # created Day 9
│   └── retrospective.md           # created Day 10
│
├── client/                        # React (Vite) frontend
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   ├── .env.example                # placeholder for VITE_API_URL
│   └── src/
│       ├── main.jsx                # app entry point
│       ├── App.jsx                 # router setup
│       ├── pages/
│       │   ├── Login.jsx
│       │   ├── Signup.jsx
│       │   ├── Diagnose.jsx
│       │   ├── History.jsx
│       │   ├── SessionDetail.jsx
│       │   └── Stats.jsx
│       ├── components/
│       │   ├── NavBar.jsx
│       │   ├── DiagnosisResult.jsx  # shared by Diagnose & SessionDetail
│       │   ├── SessionCard.jsx
│       │   ├── EmptyState.jsx       # reused by History & Stats
│       │   ├── LoadingState.jsx
│       │   └── ProtectedRoute.jsx
│       ├── context/
│       │   └── AuthContext.jsx
│       ├── api/
│       │   ├── authApi.js
│       │   ├── diagnoseApi.js
│       │   ├── sessionsApi.js
│       │   └── statsApi.js
│       └── styles/
│           └── (global styles — exact approach, e.g. CSS modules vs. a single
│             global stylesheet, is a small Day 7 decision, not needed today)
│
├── server/                        # Node.js + Express backend
│   ├── package.json
│   ├── server.js                   # entry point — starts Express app
│   ├── .env.example                # placeholder for MONGODB_URI, JWT_SECRET, ANTHROPIC_API_KEY
│   ├── routes/
│   │   ├── health.js
│   │   ├── auth.js
│   │   ├── diagnose.js
│   │   ├── sessions.js
│   │   └── stats.js
│   ├── models/
│   │   ├── User.js
│   │   └── DebugSession.js
│   ├── middleware/
│   │   └── authMiddleware.js
│   ├── services/
│   │   └── claudeService.js        # Claude API call + retry/backoff
│   └── utils/
│       ├── hash.js                 # bcrypt helpers (optional wrapper)
│       └── parseDiagnosis.js       # labeled-text parser
│
└── assets/                         # created Day 10
    └── screenshots/, demo.gif
```

---

## Why this structure

- **`client/` and `server/` as top-level siblings** (not a monorepo tool, not a single combined Next.js app): keeps the two halves of the stack fully independent, matching the two separate hosting targets (Vercel for client, Render for server) decided today. Simpler to reason about and deploy than a merged full-stack framework, given the time budget.
- **`routes/` thin, `services/` and `utils/` for logic:** route files only wire HTTP → handler; anything with real logic (the Claude call, the parser, password hashing) lives in `services/` or `utils/` so it's testable and reusable without an HTTP request in the picture.
- **`models/` matches `SCHEMA.md` exactly:** one file per Mongoose schema — `User.js` and `DebugSession.js` — nothing more, matching the two-collection design.
- **`components/` split from `pages/`:** `pages/` are route-level screens (one per entry in the UI wireframes); `components/` are the reusable pieces shared across pages (e.g., `DiagnosisResult.jsx` is used by both Diagnose and Session Detail, exactly as called out in Day 6 of the Blueprint).
- **`context/AuthContext.jsx`:** a single, obvious place for "is the user logged in" state — avoids prop-drilling auth state through every page.
- **`docs/` centralizes every planning artifact**, including this file — so anyone (including a future fresh AI session) can reconstruct full project context by reading one folder.
- **No `tests/` folder yet:** Day 8 is manual/structured testing per the Blueprint, not automated test suites — if that changes, a `tests/` folder can be added then without restructuring anything else.

## What future code will live where

- Any new v1.0 feature work (Days 4–7) only ever adds files inside `client/src/` or `server/` following the patterns above — no new top-level folders should be needed.
- Future Scope items (GitHub integration, extensions, etc.) would each warrant their own top-level folder or package later — not a concern for this capstone.
