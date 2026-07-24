# CodeLens — Project Structure

*Day 2 Deliverable, updated Day 3, Day 4, and Day 5 with build status*

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
├── client/                          # React + Vite frontend
│   ├── .env                         # not yet created — hardcoded API URL for now  ⏳
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
│       │   ├── Repos.jsx            # built Day 4 — real repo list  ✅
│       │   ├── PullRequests.jsx     # built Day 4 — real PR list    ✅
│       │   ├── PRReview.jsx         # built Day 4, wired Day 5 — real AI review + raw JSON  ✅
│       │   ├── FileUpload.jsx                                      ⏳ (Day 7)
│       │   ├── ReviewResults.jsx                                   ⏳ (Day 5-6)
│       │   ├── History.jsx                                         ⏳ (Day 7)
│       │   └── ReviewDetail.jsx                                    ⏳ (Day 7)
│       ├── components/
│       │   ├── ProtectedRoute.jsx   # built Day 3                  ✅
│       │   ├── Navbar.jsx                                          ⏳ (Day 4)
│       │   ├── ScoreChart.jsx                                      ⏳ (Day 6)
│       │   ├── FindingsList.jsx                                    ⏳ (Day 6)
│       │   ├── FindingCard.jsx                                     ⏳ (Day 6)
│       │   ├── LoadingSpinner.jsx                                  ⏳ (Day 5)
│       │   └── ErrorBanner.jsx                                     ⏳ (Day 5)
│       ├── services/
│       │   └── api.js               # built Day 4 — axios instance, withCredentials  ✅
│       └── styles/
│           └── index.css                                           ✅ (Vite default, not yet customized)
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
│   │   └── Review.js                                               ⏳ (Day 5)
│   ├── routes/
│   │   ├── auth.routes.js           # built Day 3                  ✅
│   │   ├── repos.routes.js          # built Day 4 — repos, pulls, pull files  ✅
│   │   ├── review.routes.js         # built Day 5 — POST /pr, POST /file  ✅
│   │   └── history.routes.js                                        ⏳ (Day 7)
│   ├── controllers/                                                ⏳ (Day 4+ — logic currently inline in routes)
│   ├── middleware/
│   │   ├── requireAuth.js           # built Day 3                  ✅
│   │   └── errorHandler.js                                         ⏳ (Day 4+ — inline try/catch per route today)
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
