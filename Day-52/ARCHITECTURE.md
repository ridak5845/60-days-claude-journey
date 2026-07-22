# CodeLens — System Architecture

*Day 2 Deliverable · MERN Stack · AI-Powered Multi-User Code Review Platform*

---

## 1. Tech Stack (Locked)

| Layer | Choice | Notes |
|---|---|---|
| Frontend | React 18 + Vite | SPA, no SSR needed |
| Backend | Node.js + Express | REST API, stateless |
| Database | MongoDB Atlas (M0 free) | Document store, nested findings |
| Auth | GitHub OAuth (Passport.js) + JWT | Stateless sessions |
| AI | Google Gemini API (`gemini-1.5-flash` or current free tier) | JSON-mode structured output; GitHub Models as fallback |
| Hosting | Render (backend) + Vercel/Netlify (frontend) | Free tier, auto-deploy from GitHub |
| Key libraries | mongoose, axios, recharts, multer, jsonwebtoken, passport-github2, cors, dotenv | |

---

## 2. Component Diagram

```mermaid
graph TB
    subgraph Client["Client (React + Vite) — localhost:5173"]
        A[Login Page]
        B[Repo List]
        C[PR List]
        D[Review Dashboard]
        E[History Page]
        F[Single-File Review]
        G[AuthContext]
        H[Navbar]
    end

    subgraph Server["Server (Node + Express) — localhost:5000"]
        I[auth routes]
        J[repos routes]
        K[review routes]
        L[history routes]
        M[requireAuth middleware]
        N[githubService]
        O[aiService]
        P[passport config]
    end

    subgraph External["External Services"]
        Q[(MongoDB Atlas)]
        R[GitHub OAuth + REST API]
        S[Google Gemini API]
    end

    A -->|GET /api/auth/github| I
    B -->|GET /api/repos| J
    C -->|GET /api/repos/:owner/:repo/pulls| J
    D -->|POST /api/review/pr| K
    E -->|GET /api/reviews| L
    F -->|POST /api/review/file| K

    G -.->|attaches JWT| Client

    J --> M --> N
    K --> M --> N
    K --> O
    L --> M

    I --> P --> R
    N --> R
    O --> S
    I --> Q
    K --> Q
    L --> Q
```

---

## 3. Data Flow — End-to-End Review Request

```mermaid
sequenceDiagram
    participant U as User (Browser)
    participant C as React Client
    participant S as Express Server
    participant GH as GitHub API
    participant AI as Gemini API
    participant DB as MongoDB Atlas

    U->>C: Clicks "Review this PR"
    C->>S: POST /api/review/pr {owner, repo, prNumber} + JWT
    S->>S: requireAuth middleware verifies JWT
    S->>GH: GET diff (Accept: v3.diff) using user's OAuth token
    GH-->>S: Raw unified diff
    S->>S: Truncate diff if over token budget
    S->>AI: reviewCode({ codeContent, context })
    AI-->>S: JSON {categories, findings[]}
    S->>S: Parse/validate JSON (strip fences, retry once if malformed)
    S->>DB: Save Review document (userId, repo/pr, categories, findings)
    DB-->>S: Saved review _id
    S-->>C: 200 { categories, findings, reviewId }
    C->>U: Render ScoreChart + FindingsList
```

---

## 4. Request Lifecycle — Protected Route

```mermaid
flowchart LR
    A[Incoming request] --> B{Authorization header present?}
    B -- No --> C[401 Unauthorized]
    B -- Yes --> D{JWT valid & not expired?}
    D -- No --> C
    D -- Yes --> E[Attach req.user]
    E --> F[Route handler runs]
    F --> G{Needs GitHub API?}
    G -- Yes --> H[githubService call with user's stored token]
    G -- No --> I[DB / logic only]
    H --> J[Response]
    I --> J
```

---

## 5. AI Interaction Flow (aiService)

```mermaid
flowchart TD
    A[reviewCode called with codeContent + context] --> B[Build prompt: system role + schema + few-shot example]
    B --> C[Call Gemini API]
    C --> D{Response is valid JSON matching schema?}
    D -- Yes --> E[Return structured result]
    D -- No: wrapped in code fences --> F[Strip fences, retry parse]
    F --> D
    D -- No: still invalid after 1 retry --> G[Return clean error to caller — no crash]
```

**Design note:** `aiService.reviewCode()` is intentionally the *only* function that talks to Gemini. Both `POST /api/review/pr` (Day 5) and `POST /api/review/file` (Day 7) call this same function — this was already the blueprint's plan and is confirmed as correct: it avoids prompt-logic duplication and guarantees PR reviews and file reviews always return the identical JSON shape, which is what lets `ReviewDashboard.jsx`'s `ScoreChart`/`FindingsList` components be reused for both without modification.

---

## 6. External Services

| Service | Purpose | Auth method | Rate/limits to watch |
|---|---|---|---|
| GitHub OAuth | User login | OAuth 2.0 authorization-code flow | N/A |
| GitHub REST API | Repo list, PR list, PR diff, (Day 8) inline comments | User's OAuth token (`Bearer`) | 5,000 req/hr authenticated |
| Google Gemini API | AI code review generation | API key (server-side only, never sent to client) | Free-tier requests/min — truncate large diffs to stay within token budget |
| MongoDB Atlas | Persistent storage (users, reviews) | Connection string (URI) with DB user credentials | M0 free tier: 512MB storage, shared RAM — sufficient for this project's scale |

---

## 7. Environment Variable Map

| Variable | Location | Used by |
|---|---|---|
| `PORT` | `/server/.env` | server.js |
| `MONGO_URI` | `/server/.env` | mongoose connection |
| `GEMINI_API_KEY` | `/server/.env` | aiService.js |
| `JWT_SECRET` | `/server/.env` | auth routes, requireAuth middleware |
| `GITHUB_CLIENT_ID` / `GITHUB_CLIENT_SECRET` | `/server/.env` | passport.js config |
| `GITHUB_CALLBACK_URL` | `/server/.env` | passport.js config (differs local vs. production — see Day 10) |
| `VITE_API_BASE_URL` | `/client/.env` | axios base config (points to localhost:5000 in dev, Render URL in production) |

*None of these are committed to git — confirmed via `.gitignore` during Step 0.*
