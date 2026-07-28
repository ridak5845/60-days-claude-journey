# CodeLens — Project Structure

*Day 2 Deliverable, updated Day 3, Day 4, Day 5, Day 6, Day 7, and Day 8 with build status. Both client and server are deployed and live (Render + Vercel). Core v1.0 feature-complete and production-hardened as of Day 8.*

**Status legend:** ✅ built and verified · ⏳ planned, not yet built · 🗑️ removed as dead code

```
codelens/
├── .gitignore                                                    ✅
├── README.md                                                     ✅
├── ARCHITECTURE.md                                                ✅
├── SCHEMA.md                                                      ✅
├── API.md                                                         ✅
├── UI-WIREFRAMES.md                                                ✅
├── PROJECT-STRUCTURE.md                                            ✅
├── SETUP.md                                                        ✅ (Day 3)
├── ENVIRONMENT.md                                                  ✅ (Day 3)
├── DAY3-SUMMARY.md                                                 ✅ (Day 3)
├── BLUEPRINT-ADDENDUM-DAY3-AUTH.md                                 ✅ (Day 3)
├── PROJECT-LOG.md                                                  ✅
│
├── client/                          # React + Vite frontend — deployed to Vercel (Day 6)
│   ├── .env                         # VITE_API_BASE_URL (local dev value; production value set in Vercel dashboard)  ✅
│   ├── vercel.json                  # built Day 6 — SPA rewrite so client-side routes don't 404 on refresh/direct nav  ✅
│   ├── index.html                                                 ✅
│   ├── package.json                                                ✅
│   ├── vite.config.js                                              ✅
│   └── src/
│       ├── main.jsx                                                ✅
│       ├── App.jsx                  # routing wired                ✅
│       ├── context/
│       │   └── AuthContext.jsx      # built Day 3                  ✅
│       ├── pages/
│       │   ├── LandingPage.jsx      # built Day 3                  ✅
│       │   ├── Dashboard.jsx        # removed — replaced by Repos.jsx  🗑️ (Day 4)
│       │   ├── Repos.jsx            # built Day 4, refactored Day 7 — nav moved to global NavBar  ✅
│       │   ├── PullRequests.jsx     # built Day 4, refactored Day 7 — nav moved to global NavBar  ✅
│       │   ├── PRReview.jsx         # built Day 4/5/6, refactored Day 7 — nav moved to global NavBar  ✅
│       │   ├── FileReview.jsx       # built Day 7, hardened Day 8 — char count, size guard  ✅
│       │   ├── History.jsx          # built Day 7, refactored Day 8 — uses useApiData hook  ✅
│       │   ├── HistoryDetail.jsx    # built Day 7, refactored Day 8 — uses useApiData hook  ✅
│       │   └── NotFound.jsx         # built Day 8 — clean 404 page  ✅
│       ├── hooks/
│       │   └── useApiData.js        # built Day 8 — consolidates duplicated loading/error/fetch pattern  ✅
│       ├── components/
│       │   ├── ProtectedRoute.jsx   # built Day 3                  ✅
│       │   ├── ErrorBoundary.jsx    # built Day 8 — catches uncaught render errors, prevents blank-screen crash  ✅
│       │   ├── OfflineBanner.jsx    # built Day 8 — navigator.onLine detection  ✅
│       │   ├── NavBar.jsx          # built Day 7 — persistent Repos/History/File Review nav, active-link styling  ✅
│       │   ├── ScorePanel.jsx      # built Day 6, polished Day 7 — bar + number + Good/Fair/Weak tag  ✅
│       │   ├── FindingsList.jsx    # built Day 6, polished Day 7 — aria-expanded, animated chevron  ✅
│       │   ├── FindingCard.jsx     # built Day 6, polished Day 7 — colored left border accent  ✅
│       │   ├── Footer.jsx          # built Day 6 — challenge footer, all pages  ✅
│       │   ├── LoadingSpinner.jsx                                  🗑️ (inline .spinner CSS used instead)
│       │   └── ErrorBanner.jsx     # built Day 6                    ✅
│       ├── utils/
│       │   └── diffParser.js       # built Day 6 — code excerpt extraction from patch  ✅
│       ├── services/
│       │   └── api.js               # built Day 4 — axios instance, withCredentials  ✅
│       └── styles/
│           └── index.css                                           ✅ (Day 6 base theme, Day 7 typography/motion/a11y/landing polish)
│
├── server/                          # Node + Express backend
│   ├── .env                                                        ✅
│   ├── package.json                                                ✅
│   ├── server.js                    # entry point, auth wired      ✅
│   ├── config/
│   │   ├── db.js                    # inline in server.js for now  ⏳ (extract Day 4+)
│   │   └── passport.js              # built Day 3                  ✅
│   ├── models/
│   │   ├── User.js                  # built Day 3                  ✅
│   │   └── Review.js                # built Day 7 — matches SCHEMA.md's finalized 3-score shape  ✅
│   ├── routes/
│   │   ├── auth.routes.js           # built Day 3                  ✅
│   │   ├── repos.routes.js          # built Day 4 — repos, pulls, pull files  ✅
│   │   └── review.routes.js         # built Day 5, extended Day 7 — POST /pr, POST /file (now persisting), GET /history, GET /history/:id  ✅
│   ├── controllers/                                                ⏳ (Day 4+ — logic currently inline in routes)
│   ├── middleware/
│   │   ├── requireAuth.js           # built Day 3                  ✅
│   │   └── errorHandler.js          # built Day 8 — centralized fallback error handler  ✅
│   ├── services/
│   │   ├── githubService.js         # built Day 4 — repos, pulls, pull files  ✅
│   │   └── aiReviewService.js        # built Day 5 — Gemini call, JSON parse/retry/validate  ✅
│   └── utils/
│       ├── generateToken.js         # built Day 3                  ✅
│       └── diffTruncate.js                                         ⏳ (Day 5)
│
└── docs/
    └── screenshots/                                                 ⏳
```

---

## Why this structure

- **`client/` and `server/` as top-level siblings** (not a monorepo tool like Turborepo/Nx): the project is small enough that a build-tool-managed monorepo would add setup overhead with no real payoff over 9 days. Two independently-runnable Node projects is simpler to reason about and deploy (Vercel reads `client/`, Render reads `server/`, cleanly).
- **`services/` on both sides carries a specific meaning**: on the client it's "talk to our own API"; on the server it's "talk to an external API (GitHub, Gemini)." Keeping *all* GitHub calls inside `githubService.js` and *all* AI calls inside `aiService.js` means if either external API ever needs error handling changes, there's exactly one file to touch.
- **`routes/` vs `controllers/` split**: routes just define the URL + method + which middleware runs (matches API.md 1:1); controllers hold the actual logic. This keeps route files readable as a table of contents for the whole API.
- **`middleware/errorHandler.js` exists from Day 2**, even though it won't have real logic until later days, because every route designed today (API.md) references specific HTTP error codes (401/403/404/413/502) — having one centralized place to produce them consistently avoids each controller reinventing error responses.
- **Where future code lives**: every file named above corresponds to a specific route or component already speced in API.md, SCHEMA.md, or UI-WIREFRAMES.md — nothing in this structure is speculative or "we'll figure out what goes here later."
