
## 60 Days of Claude AI Challenge — Day 58

### Capstone Project — Day 8 of 10

### **Focus:** Testing, Debugging & Production Optimization

---

## 1. Project Repository (Actual Deliverable)

**Project Repo:** https://github.com/ridak5845/codelens.git

**Day 8 Commit URL:** https://github.com/ridak5845/codelens/commit/ef5057b3f9fae727f99832cfd7dec0f57b104116

### Fixes and Optimizations Included

* Added Helmet security headers
* Added global and AI-endpoint rate limiting
* Added startup environment validation
* Added MongoDB fail-fast connection timeout
* Added centralized Express error handler
* Added strict `prNumber` validation
* Added MongoDB ObjectId validation for history routes
* Added server-side 50,000-character upload guard
* Added proper JSON 404 handling for unknown API routes
* Added React Error Boundary
* Added Offline Banner for network detection
* Added NotFound page with catch-all routing
* Added reusable `useApiData` hook
* Fixed WCAG AA contrast issue for tertiary text
* Added keyboard accessibility improvements to the upload area
* Fixed logout cookie clearing behavior

---

## 2. ABTalks Repository

**ABTalks Repo:** https://github.com/<your-username>/ABTalks

**Day 8 Documentation Commit URL:** https://github.com/<your-username>/ABTalks/commit/<abtalks-day8-commit-hash>

---

## 3. SCREENSHOTS OF THE APP

**1.LANDING PAGE**

![landing page](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-28%20045954.png)

**2.GITHUB LOGIN PAGE**

![login page](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-26%20192731.png)

**3.REPOS PAGE**

![repos](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-26%20192821.png)

**4.PULL REQUEST PAGE**

![pull request](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-26%20200120.png)

**5.PR REVIEW RESULT**

![result](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-26%20193626.png)

**6.FILE REVIEW PAGE**

![review](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-28%20195135.png)

**7.HISTORY PAGE**

![history](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-28%20195117.png)


**8.HISTORY DETAIL PAGE**

![history detail page](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-28%20000820.png)

**9.OFFLINE PAGE**

![offline](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-28%20195456.png)

**10.404 PAGE**

![404 page](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/Screenshot%202026-07-28%20195153.png)

---

## 4. Issues Found and Fixed

### Issue 1 — OAuth Login Broke After CSRF Change

**Problem:** Adding `state: true` to the GitHub OAuth strategy caused authentication to fail.

**Root Cause:** Passport OAuth2 state storage requires `req.session`, but the app uses a stateless JWT + HTTP-only cookie architecture.

**Fix:** Removed the session-dependent state configuration and documented the architectural reason in code comments.

---

### Issue 2 — Server Could Start with Missing Environment Variables

**Problem:** The backend started successfully even when required environment variables were missing, causing runtime failures later.

**Fix:** Added startup validation that checks all required variables and exits with a clear error message.

---

### Issue 3 — MongoDB Connection Hung Too Long

**Problem:** Database outages caused the server to hang for 30+ seconds before failing.

**Fix:** Added `serverSelectionTimeoutMS: 8000` to fail fast and surface the error immediately.

---

### Issue 4 — Invalid PR Numbers Reached GitHub API

**Problem:** Non-numeric values could become `NaN` and produce malformed GitHub API requests.

**Fix:** Added strict positive-integer validation before making the API call.

---

### Issue 5 — Malformed History IDs Could Crash Requests

**Problem:** Invalid MongoDB ObjectIds triggered cast errors during history detail lookups.

**Fix:** Added ObjectId format validation and returned a clean `400 Bad Request` response.

---

### Issue 6 — Unhandled React Errors Produced a Blank Screen

**Problem:** A rendering error in any page could crash the entire React app.

**Fix:** Added `ErrorBoundary.jsx` with a user-friendly recovery UI.

---

### Issue 7 — Users Had No Feedback When Offline

**Problem:** Network disconnections looked like the app was frozen.

**Fix:** Added `OfflineBanner.jsx` using `navigator.onLine` plus `online` and `offline` event listeners.

---

### Issue 8 — Small Text Failed Accessibility Contrast

**Problem:** `--text-tertiary: #6b6b76` failed WCAG AA contrast requirements on the dark background.

**Fix:** Updated the color to `#86868f`, improving the contrast ratio to approximately 4.6:1.

---

## 5. FILES

**PROJECT-STRUCTURE.md**

[PROJECT-STRUCTURE](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/PROJECT-STRUCTURE%20(7).md)

**ENVIRONMENT.md**

[ENVIRONMENT](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/ENVIRONMENT%20(2).md)

**API.md**

[API](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/API%20(4).md)

**DAY8=SUMMARY.md**

[DAY8-SUMMARY](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-58/DAY8-SUMMARY.md)

---

## 5. End-to-End Walkthrough Screenshots

### Screenshot Checklist

| Screenshot                   | Purpose                          |
| ---------------------------- | -------------------------------- |
| `01-landing-page.png`        | Public landing page              |
| `02-github-login.png`        | Successful GitHub authentication |
| `03-repositories-page.png`   | Repository listing               |
| `04-pull-requests-page.png`  | Open pull requests               |
| `05-pr-review-results.png`   | AI-generated PR review           |
| `06-file-review-page.png`    | Standalone file review           |
| `07-history-page.png`        | Review history list              |
| `08-history-detail-page.png` | Detailed saved review            |
| `09-offline-banner.png`      | Offline detection UI             |
| `10-404-page.png`            | Custom 404 page                  |

---

## 6. Key Learnings

### What I Learned Today

* **Security features can conflict with existing architecture decisions.** The OAuth regression highlighted the importance of understanding framework dependencies before adding protections.
* **Fail-fast behavior is critical for production systems.** Catching configuration and database problems at startup is far better than discovering them during user requests.
* **Edge-case validation prevents expensive debugging later.** Strict input validation eliminated several classes of malformed requests before they could reach external APIs.
* **Frontend resilience matters as much as backend correctness.** Error boundaries, offline handling, and proper 404 pages significantly improve real-world user experience.
* **Accessibility fixes are part of production readiness.** Small visual adjustments, such as contrast improvements and keyboard support, have a measurable impact on usability.

---

## 7. Final Status

### Day 8 Outcome

| Area                       | Status      |
| -------------------------- | ----------- |
| Security hardening         | ✅ Completed |
| Input validation           | ✅ Completed |
| Error handling             | ✅ Completed |
| Frontend resilience        | ✅ Completed |
| Accessibility improvements | ✅ Completed |
| End-to-end walkthrough     | ✅ Verified  |
| Documentation updates      | ✅ Completed |

**Overall:** CodeLens is now significantly more stable, secure, accessible, and production-ready than the Day 7 build.

---

## Submission Format

**Project repo commit URL:** https://github.com/ridak5845/60-days-claude-journey.git

**ABTalks repo commit URL:** https://github.com/ridak5845/60-days-claude-journey/commit/663dfec5993ee366880b166e7fb1965a6e72c9bf
