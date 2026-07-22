# CodeLens — Project Structure

*Day 2 Deliverable · Full folder layout for the 9-day build*

```
codelens/
├── .gitignore
├── README.md
├── ARCHITECTURE.md
├── SCHEMA.md
├── API.md
├── UI-WIREFRAMES.md
├── PROJECT-STRUCTURE.md
├── PROJECT-LOG.md
│
├── client/                          # React + Vite frontend
│   ├── .env                         # VITE_API_BASE_URL (not committed)
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   └── src/
│       ├── main.jsx                 # React root render
│       ├── App.jsx                  # Route definitions
│       ├── context/
│       │   └── AuthContext.jsx      # Global logged-in-user state + JWT handling
│       ├── pages/
│       │   ├── LandingPage.jsx
│       │   ├── Dashboard.jsx        # Repo list
│       │   ├── PRList.jsx
│       │   ├── FileUpload.jsx
│       │   ├── ReviewResults.jsx
│       │   ├── History.jsx
│       │   └── ReviewDetail.jsx
│       ├── components/
│       │   ├── Navbar.jsx
│       │   ├── ScoreChart.jsx       # recharts bar chart
│       │   ├── FindingsList.jsx
│       │   ├── FindingCard.jsx
│       │   ├── LoadingSpinner.jsx
│       │   └── ErrorBanner.jsx
│       ├── services/
│       │   └── api.js               # axios instance, one function per endpoint in API.md
│       └── styles/
│           └── index.css
│
├── server/                          # Node + Express backend
│   ├── .env                         # secrets — not committed
│   ├── package.json
│   ├── server.js                    # entry point, middleware wiring
│   ├── config/
│   │   ├── db.js                    # mongoose connection
│   │   └── passport.js              # GitHub OAuth strategy
│   ├── models/
│   │   ├── User.js                  # matches SCHEMA.md §2
│   │   └── Review.js                # matches SCHEMA.md §3
│   ├── routes/
│   │   ├── auth.routes.js           # matches API.md — Auth section
│   │   ├── repos.routes.js          # matches API.md — Repos & PRs
│   │   ├── review.routes.js         # matches API.md — Review section
│   │   └── history.routes.js        # matches API.md — History section
│   ├── controllers/
│   │   ├── auth.controller.js
│   │   ├── repos.controller.js
│   │   ├── review.controller.js
│   │   └── history.controller.js
│   ├── middleware/
│   │   ├── requireAuth.js           # JWT verification, matches ARCHITECTURE.md §4
│   │   └── errorHandler.js          # centralized error → HTTP status mapping
│   ├── services/
│   │   ├── githubService.js         # all GitHub REST API calls live here, nowhere else
│   │   └── aiService.js             # the ONLY module that calls Gemini — see ARCHITECTURE.md §5
│   └── utils/
│       └── diffTruncate.js          # keeps large PR diffs within AI token budget
│
└── docs/
    └── screenshots/                 # for README + LinkedIn post images
```

---

## Why this structure

- **`client/` and `server/` as top-level siblings** (not a monorepo tool like Turborepo/Nx): the project is small enough that a build-tool-managed monorepo would add setup overhead with no real payoff over 9 days. Two independently-runnable Node projects is simpler to reason about and deploy (Vercel reads `client/`, Render reads `server/`, cleanly).
- **`services/` on both sides carries a specific meaning**: on the client it's "talk to our own API"; on the server it's "talk to an external API (GitHub, Gemini)." Keeping *all* GitHub calls inside `githubService.js` and *all* AI calls inside `aiService.js` means if either external API ever needs error handling changes, there's exactly one file to touch.
- **`routes/` vs `controllers/` split**: routes just define the URL + method + which middleware runs (matches API.md 1:1); controllers hold the actual logic. This keeps route files readable as a table of contents for the whole API.
- **`middleware/errorHandler.js` exists from Day 2**, even though it won't have real logic until later days, because every route designed today (API.md) references specific HTTP error codes (401/403/404/413/502) — having one centralized place to produce them consistently avoids each controller reinventing error responses.
- **Where future code lives**: every file named above corresponds to a specific route or component already speced in API.md, SCHEMA.md, or UI-WIREFRAMES.md — nothing in this structure is speculative or "we'll figure out what goes here later."
