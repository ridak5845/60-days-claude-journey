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

### `POST /review/pr`
- **Purpose:** Fetch a PR's diff and generate an AI review.
- **Request body:** `{ owner, repo, prNumber }`
- **Response:** `200 { reviewId, categories, findings, overallScore }`
- **Validation:** All three fields required; `prNumber` must be a positive integer.
- **Auth:** Required.
- **Errors:** `400` missing/invalid fields; `401` unauthenticated; `404` PR not found; `413` diff too large (truncated and noted in response, not a hard failure — see Architecture doc §5); `502` Gemini API failure.

### `POST /review/file`
- **Purpose:** Review a single uploaded file.
- **Request:** `multipart/form-data` — field `file`.
- **Response:** `200 { reviewId, categories, findings, overallScore }`
- **Validation:** File required; max size enforced (recommend 200KB text limit — large enough for any real source file, small enough to stay well within AI token budget); allowed types: common source extensions (`.js .jsx .ts .py .java .go` etc.), rejected otherwise.
- **Auth:** Required.
- **Errors:** `400` no file / unsupported type / too large; `401`; `502` Gemini API failure.

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

- Every authenticated route uses one shared `requireAuth` middleware (see Architecture doc §4) — no route reimplements JWT verification.
- Every GitHub-API-dependent route can return `502` if GitHub itself is down or rate-limited — the frontend should show a distinct "GitHub unavailable" message rather than a generic error for this code.
- Every AI-dependent route can return `502` if Gemini fails after the one retry described in Architecture §5 — frontend shows "AI review failed, please try again" rather than a raw error.
