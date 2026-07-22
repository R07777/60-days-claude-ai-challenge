# DebugMate — API Design

Base URL (local): `http://localhost:5000/api`
Base URL (production): set on Day 9, e.g. `https://debugmate-api.onrender.com/api`

All request/response bodies are JSON. All protected routes require header:
`Authorization: Bearer <jwt>`

No implementation in this document — design only, per Day 2 scope.

---

## 1. `GET /api/health`

- **Purpose:** Confirm the API is running (built Day 3, used for deploy verification).
- **Auth:** None
- **Request:** —
- **Response 200:**
  ```json
  { "status": "ok" }
  ```
- **Error cases:** None expected; this route has no logic beyond a static response.

---

## 2. `POST /api/auth/signup`

- **Purpose:** Create a new user account (FR-1.1).
- **Auth:** None
- **Request:**
  ```json
  { "email": "user@example.com", "password": "minimum8chars" }
  ```
- **Validation:**
  - `email` required, valid email format
  - `password` required, minimum 8 characters
  - `email` must not already exist in `users`
- **Response 201:**
  ```json
  {
    "token": "<jwt>",
    "user": { "id": "...", "email": "user@example.com" }
  }
  ```
- **Error cases:**
  - `400` — missing/invalid fields (e.g., malformed email, password too short)
  - `409` — email already registered

---

## 3. `POST /api/auth/login`

- **Purpose:** Authenticate an existing user (FR-1.2).
- **Auth:** None
- **Request:**
  ```json
  { "email": "user@example.com", "password": "minimum8chars" }
  ```
- **Validation:** both fields required
- **Response 200:**
  ```json
  {
    "token": "<jwt>",
    "user": { "id": "...", "email": "user@example.com" }
  }
  ```
- **Error cases:**
  - `400` — missing fields
  - `401` — email not found or password incorrect (deliberately the same error/message for both, so login attempts can't be used to enumerate valid emails)

---

## 4. `POST /api/diagnose`

- **Purpose:** Core diagnosis engine — submit an error + code, get an AI diagnosis, and persist it (FR-2.1–2.5, FR-3.1).
- **Auth:** Required
- **Request:**
  ```json
  { "errorMessage": "IndexError: list index out of range", "codeSnippet": "print(items[5])" }
  ```
- **Validation:**
  - `errorMessage` required, non-empty, reasonable max length (e.g., 2,000 chars)
  - `codeSnippet` required, non-empty, reasonable max length (e.g., 10,000 chars)
- **Response 201:**
  ```json
  {
    "id": "665f...",
    "language": "Python",
    "errorType": "IndexError",
    "rootCause": "The list only has 3 items (indices 0-2)...",
    "fix": "Check len(items) before indexing, or use a safe access pattern...",
    "conceptExplanation": "Python list indices are zero-based and bounded...",
    "createdAt": "2026-07-22T10:15:00.000Z"
  }
  ```
- **Error cases:**
  - `400` — missing/empty fields, input exceeds max length
  - `401` — missing/invalid token
  - `502` — Claude API failed after retries (friendly message: "Diagnosis failed, please try again")

---

## 5. `GET /api/sessions`

- **Purpose:** List the logged-in user's past sessions, newest first (FR-3.2).
- **Auth:** Required
- **Request:** — (optional future query params like pagination are out of v1.0 scope; returns all sessions for the user)
- **Response 200:**
  ```json
  {
    "sessions": [
      {
        "id": "665f...",
        "language": "Python",
        "errorType": "IndexError",
        "createdAt": "2026-07-22T10:15:00.000Z"
      }
    ]
  }
  ```
  (Summary fields only — full detail is fetched per-session via endpoint 6, keeping the list lightweight.)
- **Error cases:**
  - `401` — missing/invalid token

---

## 6. `GET /api/sessions/:id`

- **Purpose:** Full detail of one past session (FR-3.3).
- **Auth:** Required
- **Request:** `:id` = session's MongoDB `_id`
- **Validation:** `:id` must be a valid ObjectId format
- **Response 200:** same shape as `POST /api/diagnose`'s response, plus `errorMessage` and `codeSnippet`.
- **Error cases:**
  - `400` — malformed `:id`
  - `401` — missing/invalid token
  - `403` — session exists but belongs to a different user (never reveal existence to a non-owner)
  - `404` — no session with that id

---

## 7. `GET /api/stats`

- **Purpose:** Aggregate dashboard data for the logged-in user (FR-4.1–4.4).
- **Auth:** Required
- **Request:** —
- **Response 200:**
  ```json
  {
    "byErrorType": [{ "errorType": "IndexError", "count": 4 }],
    "byLanguage": [{ "language": "Python", "count": 6 }],
    "overTime": [{ "date": "2026-07-21", "count": 2 }],
    "totalSessions": 8
  }
  ```
  If `totalSessions` is 0 or 1, the frontend renders the partial/empty state instead of charts (FR-4.4) — the API still returns valid (possibly empty) arrays rather than an error.
- **Error cases:**
  - `401` — missing/invalid token

---

## 8. Logout (client-side only — no endpoint)

Because auth is stateless JWT, "logout" is simply the client deleting its stored token and redirecting to Login (FR-1.4). No `/api/auth/logout` route is needed for v1.0.

---

## 9. Cross-Cutting Rules (apply to every protected route)

- Missing or malformed `Authorization` header → `401` before any route logic runs (enforced once, centrally, in `authMiddleware`).
- Expired/invalid JWT → `401`.
- Any resource lookup (`sessions/:id`) always filters by both the resource id **and** the authenticated `userId` — ownership is enforced at the query level, not just checked after fetching, per NFR-Security.
- All error responses share a consistent shape:
  ```json
  { "error": "Human-readable message" }
  ```
  so the frontend can render error states generically.
