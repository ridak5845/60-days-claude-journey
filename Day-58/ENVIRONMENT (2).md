# CodeLens — Environment Reference

*Day 3 Deliverable · All environment variables, tools, and configuration in one place*

---

## 1. Backend Environment Variables (`server/.env`)

| Variable | Example shape | Used by | Notes |
|---|---|---|---|
| `PORT` | `5000` | `server.js` | Local dev port |
| `MONGO_URI` | `mongodb://user:pass@host1:27017,host2:27017,host3:27017/codelens?ssl=true&replicaSet=...&authSource=admin` | `server.js` (mongoose.connect) | Standard (non-SRV) format — SRV format (`mongodb+srv://`) caused DNS `ECONNREFUSED` in this environment; see `DAY3-SUMMARY.md`. Database name must be `codelens`, placed right after the final `/`. |
| `GITHUB_CLIENT_ID` | `Ov23liCNcgcablFqezhV`-style string | `config/passport.js` | Not secret — safe to display/share |
| `GITHUB_CLIENT_SECRET` | long random string | `config/passport.js` | **Secret** — never commit, screenshot, or share |
| `GITHUB_CALLBACK_URL` | `http://localhost:5000/api/auth/github/callback` | `config/passport.js` | Must exactly match the URL registered in the GitHub OAuth App settings — including protocol and port |
| `GEMINI_API_KEY` | `AQ.xxxxxxxxxxxxx`-style string | `services/aiService.js` (Day 5) | **Secret** — treat like a password |
| `JWT_SECRET` | 64-char hex string | `utils/generateToken.js`, `middleware/requireAuth.js` | Generate via `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"` — never reuse a real word/phrase |

## 2. Frontend Environment Variables (`client/.env`)

Not yet created — the API base URL is currently hardcoded to `http://localhost:5000` inside `AuthContext.jsx`. Before Day 10 deployment, this should move to:

| Variable | Purpose |
|---|---|
| `VITE_API_BASE_URL` | Backend URL — `http://localhost:5000` in dev, the deployed Render URL in production |

## 3. Auth Mechanism Configuration Summary

- **Approach:** Stateless JWT delivered via HTTP-only cookie (confirmed Day 2, re-confirmed Day 3 — see Blueprint Addendum).
- **No session store, no `express-session`, no `SESSION_SECRET`** — this project does not use Passport sessions.
- Cookie settings (dev): `httpOnly: true`, `sameSite: 'lax'`, `secure: false`.
- Cookie settings (production, Day 10): `sameSite: 'none'`, `secure: true` — required once frontend and backend are on different HTTPS domains.

## 4. Tooling Versions & Setup

| Tool | Role |
|---|---|
| Node.js (v18+) | JS runtime for both client and server |
| npm | Package manager |
| Vite | Frontend dev server + build tool |
| Git / GitHub | Version control + OAuth identity provider |
| MongoDB Atlas (M0 free tier) | Hosted database |
| Google AI Studio | Gemini API key management |

## 5. Key npm Packages Installed (Day 2–3)

**Backend (`server`):**
`express`, `mongoose`, `dotenv`, `cors`, `nodemon` (dev), `passport`, `passport-github2`, `jsonwebtoken`, `cookie-parser`

**Frontend (`client`):**
`react`, `react-dom`, `axios`, `react-router-dom`

## 6. Security Notes on Record

- A Gemini API key was inadvertently shared in plaintext via screenshot during this session on Day 3. Rotation was recommended (delete + regenerate in Google AI Studio) — confirmed completed before Day 5.
- All secrets live only in `server/.env`, confirmed excluded from git via `.gitignore` and verified with `git status` showing a clean tree.

## 7. Production Environment Variables (Deployed Day 6)

**Render (`codelens-backend` service) — Environment tab:**

| Variable | Value | Notes |
|---|---|---|
| `MONGO_URI` | Same Atlas connection string as local | Requires Atlas Network Access set to **Allow Access from Anywhere** (`0.0.0.0/0`) — Render's free tier has no fixed IP |
| `GITHUB_CLIENT_ID` | Production OAuth App's Client ID | Separate OAuth App from the local one — see below |
| `GITHUB_CLIENT_SECRET` | Production OAuth App's Client Secret | |
| `GITHUB_CALLBACK_URL` | `https://codelens-backend-rien.onrender.com/api/auth/github/callback` | Must exactly match the callback URL registered on the production GitHub OAuth App |
| `GEMINI_API_KEY` | Same key as local | |
| `JWT_SECRET` | Same value as local | |
| `CLIENT_URL` | `https://codelens-beige-two.vercel.app` | Used for CORS origin and the post-login redirect target |
| `NODE_ENV` | `production` | Switches cookie settings to `sameSite: 'none'`, `secure: true` (required for cross-domain cookies) |

**Vercel (`codelens` project) — Settings → Environment Variables:**

| Variable | Value | Notes |
|---|---|---|
| `VITE_API_BASE_URL` | `https://codelens-backend-rien.onrender.com/api` | **Must not be marked "Sensitive"** — sensitive vars can't be viewed again after saving, which made debugging much harder. Baked in at build time; changing it requires a full redeploy, not just a save. |

**Production GitHub OAuth App:** a *second*, separate OAuth App named "CodeLens Production" was created (GitHub OAuth Apps only support one callback URL each) — the original local-dev OAuth App from Day 3 is untouched and still works for `localhost`.

**Deployment platforms:** Render (backend, free tier) and Vercel (frontend, free tier), both configured to auto-deploy on every push to `main`.

**Critical lesson learned:** editing files locally does nothing in production — Render and Vercel only rebuild from what's actually committed and pushed to GitHub. An hour of deployment debugging on Day 6 was ultimately traced to code changes that were written but never pushed.

## 8. Production Hardening (Day 8)

**New backend packages:** `helmet` (security headers), `express-rate-limit` (global + review-specific rate limiting to protect the free-tier Gemini quota from abuse).

**Behavioral changes, no new env vars required:**
- Server now fails loudly at startup with a clear message if any required env var is missing, instead of starting in a broken state.
- `mongoose.connect` now has an 8-second `serverSelectionTimeoutMS` — fails fast on a DB outage instead of hanging 30+ seconds (root-caused the exact issue debugged on Day 3).
- `app.set('trust proxy', 1)` added — required for correct secure-cookie and IP-detection behavior behind Render's reverse proxy.
- Passport's `state: true` CSRF option was tried and reverted — it requires `req.session`, which this app's stateless-JWT architecture (Day 3) intentionally doesn't have. The OAuth `client_secret` handshake remains the primary protection.
- Logout's `clearCookie` call now passes the same `sameSite`/`secure` attributes used when the cookie was set — some browsers otherwise silently fail to clear it.
- `--text-tertiary` CSS variable changed from `#6b6b76` to `#86868f` — the original failed WCAG AA contrast (3.2:1) on the dark background at small text sizes; the new value passes (~4.6:1).

