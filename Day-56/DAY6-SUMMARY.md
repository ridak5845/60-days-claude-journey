# CodeLens — Day 6 Summary

*Dashboard UI + Deployed, Working MVP Demo*

---

## ✅ What Was Completed Today

**Dashboard UI (blueprint's actual Day 6 scope)**
- `ScorePanel.jsx` — horizontal bar chart (recharts) for security/performance/maintainability, color-coded by score range.
- `FindingsList.jsx` + `FindingCard.jsx` — findings grouped into 4 collapsible categories (Bugs/Security/Performance/Code Quality), each showing a real code excerpt via a new `diffParser.js` utility.
- `ErrorBanner.jsx` — reusable, clean error display.
- `Footer.jsx` — the required "Built with Claude..." text, placed outside `<Routes>` so it renders on every page.
- Full dark-theme `index.css` rewrite for visual consistency across Landing, Repos, PullRequests, and PRReview pages.
- All verified via UI checkpoint: score chart rendering, all 4 categories with correct empty states, collapse/expand working, error state clean on a simulated server failure, footer visible everywhere.

**Live Deployment (agreed scope: Day 6 UI + deploy only, history/single-file deferred to Day 7 as originally planned)**
- Backend deployed to **Render** (`codelens-backend-rien.onrender.com`), frontend to **Vercel** (`codelens-beige-two.vercel.app`), both auto-deploying from GitHub pushes.
- A second, production-only GitHub OAuth App created (OAuth Apps support only one callback URL each).
- Code updated to use environment variables instead of hardcoded `localhost` URLs (`api.js`, `AuthContext.jsx`, `LandingPage.jsx`, `server.js`, `auth.routes.js`), with production-safe cookie settings (`sameSite: 'none'`, `secure: true`).

**Live, verified end-to-end on the real deployed URL:** GitHub login → real dashboard with real repos → real PR list → real AI review with score chart and findings, on `https://codelens-beige-two.vercel.app`.

---

## 🐞 Deployment Debugging Log (valuable reference for future deploys)

Four distinct issues stacked on top of each other today — each fixed and verified before moving to the next:

1. **Vercel env var never saved** — the initial `VITE_API_BASE_URL` add during project import silently didn't take; confirmed via an empty Environment Variables list.
2. **Sensitive variable can't be re-viewed** — marking it "Sensitive" hid the value permanently (one-way setting); had to delete and recreate as non-sensitive to confirm it visually.
3. **Root cause of the whole `localhost` persistence:** the actual code fix (Milestone A: `import.meta.env.VITE_API_BASE_URL` instead of hardcoded `localhost`) was written and saved **locally only** — never committed and pushed to GitHub. Both Render and Vercel build directly from GitHub, so every redeploy faithfully rebuilt the *old* code regardless of dashboard settings. This was the single biggest time cost today — a strong reminder that "I saved the file" and "the file is deployed" are two different things.
4. **MongoDB Atlas IP whitelist** — Render's servers use dynamic IPs not covered by the original Day 2 "current IP only" setting; fixed with "Allow Access from Anywhere."
5. **Vercel SPA routing 404** — direct navigation/redirect to `/dashboard` 404'd because Vercel served it as a literal file path; fixed with a `vercel.json` rewrite rule routing all paths to `index.html` for React Router to handle client-side.

---

## 📄 Files Updated Since Day 5

| File | What changed |
|---|---|
| `PROJECT-STRUCTURE.md` | Marked all Day 6 dashboard components built; added `vercel.json`; noted both services are now live/deployed. |
| `ENVIRONMENT.md` | Added a full new section documenting every production environment variable (Render + Vercel), the second production OAuth App, and the "commit before deploy matters" lesson. |

No changes were needed to `SCHEMA.md` or `API.md` today — no database or endpoint changes, purely frontend + deployment configuration.

---

## 🚧 What Still Needs Polishing

- Gemini's free-tier responses aren't perfectly deterministic between runs on the same PR — expected AI behavior, not a bug, but worth knowing before recording any demo video.
- No in-app search for public repos outside your own account — testing against repos like `facebook/react` currently requires typing the URL path directly. Low priority, not blocking.

## 🎯 Tomorrow's Objective (Day 7)

Review History + Single-File Review Mode — the two remaining pieces for a true feature-complete v1.0 per the PRD. This is also when the `Review` schema shape flagged back on Day 5 gets resolved, since history requires persisting reviews to MongoDB for the first time.

## Carried Forward

- ⚠️ Reconcile the `Review` schema shape (3 scores vs. originally-designed 4 category scores) when building persistence on Day 7.
