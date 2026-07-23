# Blueprint Addendum — Day 3 Auth Mechanism Deviation

*Supersedes: "Implementation_Blueprint_Days_2-10.docx", Day 3 section, steps 2–13 (Passport session configuration)*

*Reason for this addendum instead of editing the original .docx directly: preserves the original document's formatting/history while keeping a clear, auditable record of what changed and why.*

---

## What the original blueprint specified

Day 3's plan called for traditional Passport.js **session-based** authentication:
- `express-session` middleware
- `passport.session()`, `serializeUser()` / `deserializeUser()`
- A `SESSION_SECRET` environment variable
- Session state stored server-side (in-memory by default)

## What was actually built, and why

On Day 2, before this updated blueprint existed, **JWT-based stateless authentication** was designed and approved: GitHub OAuth → issue a custom JWT → deliver it via an **HTTP-only cookie** → verify it per-request with a stateless `requireAuth` middleware. No server-side session state.

When the updated 10-day blueprint surfaced the session-based approach on Day 3, this was flagged as a conflict per the standing project rule ("if any design decision conflicts with the approved blueprint, explain why and ask for approval before changing it"). The trade-off was presented directly, and the decision was made to **keep JWT-in-cookie** rather than switch to sessions, for one primary reason:

> **Render's free tier spins down idle backend instances.** An in-memory session store (the default, and the simplest path the original blueprint's steps implied) would silently log every user out on every cold start — a real risk for a portfolio project a recruiter might visit after it's sat idle. Avoiding a session store entirely sidesteps this failure mode without needing to add and configure `connect-mongo` or an equivalent persistent session backend.

## Concrete differences from the original Day 3 steps

| Original blueprint (steps 1–13) | What was actually built |
|---|---|
| `npm install passport passport-github2 express-session` | `npm install passport passport-github2 jsonwebtoken cookie-parser` |
| `SESSION_SECRET` in `.env` | `JWT_SECRET` in `.env` |
| `serializeUser` / `deserializeUser` in `passport.js` | Not used — Passport only handles the GitHub handshake; our own JWT logic takes over immediately after |
| `app.use(passport.session())` | Not used — `app.use(passport.initialize())` only |
| Session cookie set automatically by `express-session` | JWT manually signed (`utils/generateToken.js`) and set as an HTTP-only cookie in the OAuth callback route |
| `GET /api/auth/logout` destroys server-side session | `POST /api/auth/logout` clears the client cookie (`res.clearCookie`) — no server-side state to destroy |

## Impact on other Day 2/3 documents

- **`ARCHITECTURE.md` §4 (Request Lifecycle)** — already written assuming JWT verification; no change needed.
- **`API.md` — Auth section** — already written assuming a JWT cookie; already updated Day 2 to reflect the cookie-delivery decision.
- **`SCHEMA.md`** — no change; the `User` model never included session-related fields.

## Going forward

All remaining days (4–10) that reference "the logged-in user" or "protected routes" should assume: **JWT read from an HTTP-only cookie, verified by `requireAuth` middleware** — not a Passport session, and not `req.isAuthenticated()` (which requires sessions and will not work here).
