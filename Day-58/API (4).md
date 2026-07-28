# CodeLens — API Design (v1.0)

*Day 2 Deliverable · Design only — no implementation yet*

Base URL (dev): `http://localhost:5000/api`
All routes except `/auth/*` require header: `Authorization: Bearer <JWT>`

---

## Auth

### `GET /auth/github`
- **Purpose:** Kick off GitHub OAuth login.
- **Request:** No body. Browser redirect.
- **Response:** 302 redirect to GitHub's authorization screen.
- **Auth:** None (this *is* the entry point).
- **Errors:** None expected — GitHub handles failures on its own screen.

### `GET /auth/github/callback`
- **Purpose:** GitHub redirects here after user approves. Server exchanges code for token, creates/updates the user, issues our own JWT.
- **Request:** Query param `code` (from GitHub).
- **Response:** Sets the JWT as an **HTTP-only cookie** (`httpOnly: true`, `sameSite: 'lax'` in dev / `'none'` + `secure: true` in production per Day 10 deployment), then 302 redirects to `http://localhost:5173/dashboard`. The token never appears in the URL, browser history, or referrer headers.
- **Validation:** `code` must be present.
- **Auth:** None (Passport middleware handles the OAuth handshake itself).
- **Errors:** `401` if GitHub denies/cancels; `500` if token exchange fails.

### `GET /auth/me`
- **Purpose:** Return the logged-in user's profile (used on app load to check session validity).
- **Request:** None.
- **Response:** `200 { id, username, avatarUrl }`
- **Auth:** Required.
- **Errors:** `401` if JWT missing/invalid/expired.

### `POST /auth/logout`
- **Purpose:** Client-side token discard confirmation (stateless JWT — mostly a formality endpoint, or clears the cookie if cookie-based).
- **Request:** None.
- **Response:** `200 { message: "Logged out" }`
- **Auth:** Required.
- **Errors:** None expected.

> **Decision confirmed:** JWT delivery via HTTP-only cookie, approved. `requireAuth` middleware (server-side) reads the token from `req.cookies.token` rather than the `Authorization` header. Client-side `axios` instance must be configured with `withCredentials: true` so the cookie is sent automatically on every request — no manual header attachment needed in `api.js`.

---

## Repos & PRs

*Note: implemented under the `/api/repos` prefix (not `/api/github` as the 10-Day Blueprint's Day 4 section names it) — kept consistent with this document's original naming and the `*.routes.js` pattern established Day 3. Functionally identical.*

### `GET /repos`
- **Purpose:** List the authenticated user's GitHub repos.
- **Request:** Optional query `?page=1`.
- **Response:** `200 [{ id, name, owner, fullName, description, private, updatedAt }]`
- **Auth:** Required.
- **Errors:** `401` unauthenticated; `502` if GitHub API call fails.

### `GET /repos/:owner/:repo/pulls`
- **Purpose:** List open PRs for a given repo.
- **Request:** Path params `owner`, `repo`.
- **Response:** `200 [{ number, title, author, createdAt, updatedAt, htmlUrl }]`
- **Auth:** Required.
- **Errors:** `401`; `502` if GitHub API fails. (GitHub returns an empty array, not a 404, for a valid repo with zero open PRs — handled as an empty state on the frontend, not an error.)

### `GET /repos/:owner/:repo/pulls/:number/files`
- **Purpose:** Fetch the changed files (with patches) for a specific PR — the data Day 5's AI review engine consumes.
- **Request:** Path params `owner`, `repo`, `number`.
- **Response:** `200 [{ filename, status, additions, deletions, changes, patch }]`
- **Auth:** Required.
- **Errors:** `401`; `502` if GitHub API fails.

---

## Review

## Review

*Built Day 5, persistence added Day 7. Note: package is `@google/genai` (the current, actively-maintained, free-tier SDK), not `@google/generative-ai` as originally planned — that package was deprecated by Google in 2025. Model used is the `gemini-flash-latest` alias rather than a hardcoded version, so the app automatically stays on Google's current free-tier Flash model as they rotate versions.*

### `POST /review/pr`
- **Purpose:** Fetch a PR's diff, generate an AI review, and persist it.
- **Request body:** `{ owner, repo, prNumber }`
- **Response:** `200 { reviewId, scores: { security, performance, maintainability }, findings: [{ category, file, line, message }] }`
- **Validation:** All three fields required.
- **Auth:** Required.
- **Errors:** `400` missing fields or no changed files found; `401` unauthenticated; `502` AI or GitHub failure (generic "AI review failed. Please try again shortly." message — internal error detail is logged server-side only, never sent to the client).
- **Side effect (Day 7):** saves a `Review` document (`source: 'pr'`) before responding.

### `POST /review/file`
- **Purpose:** Review a single pasted/uploaded file's code, and persist it.
- **Request body:** `{ filename, code }`
- **Response:** Same shape as `/review/pr`, plus `reviewId`.
- **Validation:** `filename` and `code` required.
- **Auth:** Required.
- **Errors:** `400` missing fields; `401`; `502` AI failure.
- **Side effect (Day 7):** saves a `Review` document (`source: 'file'`) before responding.

### `GET /review/history`
- **Purpose:** List the logged-in user's past reviews, most recent first, summary fields only.
- **Response:** `200 [{ _id, source, repoOwner, repoName, prNumber, fileName, scores, createdAt }]`
- **Auth:** Required.
- **Errors:** `401`; `500` on query failure.
- **Security:** filters by `userId: req.user.id` directly in the MongoDB query — verified, not just assumed.

### `GET /review/history/:id`
- **Purpose:** Fetch one full saved review (including all findings).
- **Response:** `200 { full Review document }`
- **Auth:** Required — the query itself filters by both `_id` **and** `userId`, so requesting another user's review ID returns `404`, not another user's data.
- **Errors:** `401`; `404` not found or not owned by the requesting user.

---

## History

### `GET /reviews`
- **Purpose:** List the logged-in user's past reviews, most recent first.
- **Request:** Optional query `?page=1&limit=10`.
- **Response:** `200 { total, page, results: [{ id, mode, repoName, prNumber, fileName, overallScore, createdAt }] }`
- **Auth:** Required.
- **Errors:** `401`.

### `GET /reviews/:id`
- **Purpose:** Fetch full detail (all findings) for one past review.
- **Request:** Path param `id`.
- **Response:** `200 { full review document }`
- **Auth:** Required — **and** must own the review (`review.userId === req.user.id`), otherwise `403`.
- **Errors:** `401`; `403` not the owner; `404` not found.

---

## Health

### `GET /health`
- **Purpose:** Uptime/connectivity check (already implemented in Step 0 skeleton).
- **Response:** `200 { status: "ok" }`
- **Auth:** None.

---

## Cross-cutting rules (apply to all routes above)

*Hardened Day 8:*
- All `/api/*` routes are subject to a global rate limit (300 requests / 15 min per IP). `POST /review/pr` and `POST /review/file` additionally share a stricter limit (20 requests / 15 min per IP) to protect the free-tier Gemini quota specifically. Exceeding either returns `429` with a clear message.
- Request body size capped at 1MB globally; pasted/uploaded code is separately capped at 50,000 characters with a clear `400` error if exceeded (checked both client-side, with a live character counter, and server-side).
- `prNumber` is strictly validated as a positive integer server-side (previously a non-numeric value could silently become `NaN` and reach GitHub's API malformed).
- History detail lookups validate the `:id` param is a well-formed MongoDB ObjectId before querying, returning a clean `400` instead of crashing on a malformed ID.
- Any error not explicitly handled by a route falls through to a centralized error handler, which always returns clean generic JSON — never a raw stack trace.

- Every authenticated route uses one shared `requireAuth` middleware (see Architecture doc §4) — no route reimplements JWT verification.
- Every GitHub-API-dependent route can return `502` if GitHub itself is down or rate-limited — the frontend should show a distinct "GitHub unavailable" message rather than a generic error for this code.
- Every AI-dependent route can return `502` if Gemini fails after the one retry described in Architecture §5 — frontend shows "AI review failed, please try again" rather than a raw error.
