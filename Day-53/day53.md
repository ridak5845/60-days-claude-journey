# CodeLens — Day 3 Package 

## 60 Days of Claude AI Challenge 

## Hello World — Working Application

![CodeLens Hello World](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-53/Screenshot%202026-07-23%20180835.png)

![COdeLens](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-53/Screenshot%202026-07-23%20180835.png)

*GitHub OAuth login → JWT cookie issued → MongoDB user record created → protected dashboard rendered with the real logged-in username.*

---

**SETUP.md**
[SETUP](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-53/SETUP.md)

**PROJECT-STRUCTURE.md**
[PROJECT-STRUCTURE](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-53/PROJECT-STRUCTURE%20(2).md)

**ENVIRONMENT.md**
[ENVIRONMENT](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-53/ENVIRONMENT.md)

**DAY3-SUMMARY**
[SUMMARY](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-53/DAY3-SUMMARY.md)

**BLUEPRINT-ADDENDUM-DAY3-AUTH.md**
[BLUEPRINT](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-53/BLUEPRINT-ADDENDUM-DAY3-AUTH.md)

## Key Learnings

1. **Stateless auth beats sessions on free-tier hosting.** Render's free tier spins down idle instances — an in-memory session would silently log users out on every cold start. JWT-in-cookie avoids this with zero server-side session state.
2. **Verify, don't eyeball.** A recurring MongoDB `bad auth` error was actually a literal `<password>` placeholder still in the connection string — invisible in screenshots, found only by printing raw character codes.
3. **OS-level and runtime DNS can disagree.** `nslookup` succeeded while Node's resolver failed the same `mongodb+srv://` lookup — a Windows/network-specific quirk, fixed by switching to the standard `mongodb://` multi-host string.
4. **Surface conflicts before building on them.** An updated blueprint proposed session-based auth conflicting with the already-approved JWT design — flagging the trade-off explicitly kept the project's documentation trustworthy.
5. **Treat any exposed API key as compromised immediately** — including screenshots shared for debugging. Rotate first, ask questions later.

---

## Setup — Quick Reference

**Prerequisites:** Node.js 18+, npm, Git, MongoDB Atlas account, GitHub account, Google AI Studio account.

**Install:**
```bash
git clone https://github.com/ridak5845/codelens.git
cd codelens/server && npm install
cd ../client && npm install
```

**`server/.env` required variables:** `PORT`, `MONGO_URI`, `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, `GITHUB_CALLBACK_URL`, `GEMINI_API_KEY`, `JWT_SECRET` — full details and generation commands in `ENVIRONMENT.md`.

**Run (two terminals):**
```bash
cd server && npm run dev   # → MongoDB connected, port 5000
cd client && npm run dev   # → port 5173
```

**Verify:** visit `localhost:5173` → Sign in with GitHub → land on `/dashboard` with your real username → `localhost:5000/api/health` returns `{"status":"ok"}`.

*Full step-by-step setup, including MongoDB Atlas and GitHub OAuth App registration, is in the standalone `SETUP.md`.*


## Project Structure — What Exists Today

```
codelens/
├── client/src/
│   ├── context/AuthContext.jsx     ✅ global auth state
│   ├── pages/LandingPage.jsx       ✅ GitHub login link
│   ├── pages/Dashboard.jsx         ✅ placeholder, real repo list Day 4
│   ├── components/ProtectedRoute.jsx ✅ auth-gated routing
│   └── App.jsx                     ✅ routing wired
│
├── server/
│   ├── models/User.js               ✅ matches SCHEMA.md
│   ├── config/passport.js           ✅ GitHub OAuth strategy (stateless)
│   ├── middleware/requireAuth.js    ✅ JWT verification, shared by all protected routes
│   ├── routes/auth.routes.js        ✅ /github, /callback, /me, /logout
│   ├── utils/generateToken.js       ✅ JWT signing
│   └── server.js                    ✅ CORS + cookieParser + passport wired in
│
└── docs/, ARCHITECTURE.md, SCHEMA.md, API.md, UI-WIREFRAMES.md — all from Day 2, unchanged
```

Still to build (Day 4+): `githubService.js`, `repos.routes.js`, real repo/PR UI, `Review` model, AI service. Full annotated tree in the standalone `PROJECT-STRUCTURE.md`.

---

## Day 3 Summary

**Completed:** GitHub OAuth App + Gemini key set up; JWT-vs-session conflict resolved in favor of JWT (see `BLUEPRINT-ADDENDUM-DAY3-AUTH.md`); full backend auth (model, strategy, JWT helper, middleware, routes) and frontend auth scaffold (context, routing, protected routes, login page) built and verified end-to-end.

**Resolved today:** MongoDB DNS/`ECONNREFUSED` issue (switched from `mongodb+srv://` to standard `mongodb://`), and a `bad auth` failure caused by a leftover `<password>` placeholder.

**Open item:** confirm the exposed Gemini API key was rotated before Day 5.

**Tomorrow (Day 4):** GitHub API integration — real repo listing, PR listing, diff fetching — replacing the Dashboard placeholder with live data. `API.md` already specs the endpoints; no planning needed, straight into code.
