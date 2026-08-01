# DebugMate — Future Scope

This isn't a generic feature wishlist. Each item below was deliberately scoped *out* of v1.0 during Day 1-2 planning specifically to protect the 9-day build, and is revisited here now that a v1.0 baseline actually exists to build on.

---

## Next 3 Months — Depth Over Breadth

The goal here is making the *existing* core loop smarter, not adding new surface area.

- **GitHub repo integration.** The original "Repo Doctor" concept from Day 1's idea comparison, never built. Let a user paste a GitHub URL alongside an error; pull the relevant file(s) via the GitHub API for real multi-file context before diagnosing. This was explicitly rejected on Day 1 as too risky for a 10-day timeline — it's the single highest-value thing to build first with more time.
- **Editable/regenerate diagnosis.** If the AI's first fix isn't quite right, let the user refine the prompt ("try again, focusing on performance") rather than starting over.
- **Snippet export.** One-click copy of the Fix section as Markdown or a raw code block — small, but directly requested-and-declined on Day 1.
- **Session tagging/search.** The History page currently only sorts by date. Free-text search and manual tags were both explicitly deferred on Day 1's scope table.

## Next 6 Months — From Personal Tool to Shared Tool

- **VS Code extension.** Bring the diagnosis loop into the editor directly, using the same `POST /api/diagnose` endpoint already built — no backend changes needed, just a new client.
- **Social login (Google/GitHub OAuth).** Lower the signup friction that the current email/password flow has.
- **Team workspaces.** A bootcamp cohort or study group sharing a debugging journal — the `debugSessions` schema would need a `visibility` field and a `teamId`, a moderate but non-breaking schema change.
- **Password reset flow.** A real gap in v1.0 (deliberately deferred) that becomes necessary the moment real strangers, not just test accounts, start using it.

## Next 12 Months — A Genuine Learning Product

- **Spaced-repetition review.** The Stats dashboard already tracks error-type frequency — extend it to actively resurface "you keep making TypeError mistakes, here's a 2-minute refresher" rather than just displaying a static chart.
- **Multi-language curriculum mapping.** Tie recurring error patterns to actual learning resources (freeCodeCamp modules, official docs) instead of just an AI explanation.
- **Team/classroom analytics.** For the "bootcamp/study group" persona from the PRD's secondary user segment — an instructor view of common mistakes across a cohort.
- **Monetization exploration**, if usage justifies it — explicitly out of scope for v1.0, and the PRD's Section 5.2 exclusion list, revisited only once there's real usage data to justify it.

---

*Every item above traces back to an actual decision made during the 10-day build (see `docs/DebugMate_PRD.docx` Section 5.2 and the Day 1 idea-comparison discussion), not a generic roadmap template.*
