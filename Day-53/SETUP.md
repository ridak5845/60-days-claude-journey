# CodeLens — Setup Guide

*Day 3 Deliverable · Everything needed to get this project running from scratch*

This document lets a fresh machine (or a fresh AI session) get CodeLens running locally with no prior context beyond this file.

---

## 1. Prerequisites (Runtime & Tools)

| Tool | Why it's needed |
|---|---|
| **Node.js** (v18+) | Runs both the Express backend and the Vite dev server. Everything else depends on this being installed first. |
| **npm** (bundled with Node) | Installs all project dependencies (`express`, `react`, `passport`, etc.) |
| **Git** | Version control; connects local work to the GitHub repo |
| **A code editor** (e.g. VS Code) | Editing all project files |
| **PowerShell / terminal** | Running all commands below |

No global CLI installs are required (no Vite CLI, no framework-specific CLI) — `npm create vite@latest` and `npm install` handle everything per-project.

---

## 2. External Accounts Required

| Service | Purpose | Free tier? |
|---|---|---|
| GitHub account | Repo hosting + OAuth login provider | Yes |
| MongoDB Atlas | Database | Yes (M0 tier) |
| Google AI Studio | Gemini API key (used starting Day 5) | Yes |

---

## 3. Clone & Install

```bash
git clone https://github.com/ridak5845/codelens.git
cd codelens

cd server
npm install

cd ../client
npm install
```

---

## 4. Environment Variables

Create `server/.env` (never commit this — see `ENVIRONMENT.md` for the full variable reference):

```
PORT=5000
MONGO_URI=<your MongoDB Atlas connection string>
GITHUB_CLIENT_ID=<from GitHub OAuth App>
GITHUB_CLIENT_SECRET=<from GitHub OAuth App>
GITHUB_CALLBACK_URL=http://localhost:5000/api/auth/github/callback
GEMINI_API_KEY=<from Google AI Studio>
JWT_SECRET=<random 32+ byte string — generate with the command below>
```

Generate a secure `JWT_SECRET`:
```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

`client/.env` is not required yet — the API base URL is currently hardcoded to `http://localhost:5000` in `AuthContext.jsx`; this will move to `VITE_API_BASE_URL` before deployment (Day 10).

---

## 5. External Service Setup (one-time)

### MongoDB Atlas
1. Register at mongodb.com/cloud/atlas, create a free **M0** cluster.
2. Create a database user under **Database Access**.
3. Under **Network Access**, allow your current IP (or `0.0.0.0/0` for development).
4. Get the connection string via **Connect → Drivers**. If `mongodb+srv://` fails with a DNS/`ECONNREFUSED` error (a known Windows/network quirk — see `DAY3-SUMMARY.md`), use the standard `mongodb://` multi-host string instead, found in the same Drivers panel.
5. Append your database name (`codelens`) right after `.net/` and before the `?` query string.

### GitHub OAuth App
1. github.com/settings/developers → **OAuth Apps** → **New OAuth App**.
2. Homepage URL: `http://localhost:5173`. Callback URL: `http://localhost:5000/api/auth/github/callback` (must match exactly).
3. Copy the Client ID and generate/copy the Client Secret (shown once).

### Google Gemini API Key
1. aistudio.google.com → **Get API key** → **Create API key**.
2. Copy into `.env`. Treat this like a password — never screenshot or paste it anywhere outside your own `.env` file.

---

## 6. Running the Project

Two terminals, run simultaneously:

```bash
# Terminal 1
cd server
npm run dev
# → "Server running on http://localhost:5000"
# → "MongoDB connected"

# Terminal 2
cd client
npm run dev
# → local dev URL, typically http://localhost:5173
```

---

## 7. Verifying It Works

1. Visit `http://localhost:5173` — should show the CodeLens landing page.
2. Click **Sign in with GitHub** — should redirect to GitHub's consent screen.
3. After approving, should land on `/dashboard` showing your real GitHub username.
4. `http://localhost:5000/api/health` should return `{ "status": "ok" }` directly in the browser.
