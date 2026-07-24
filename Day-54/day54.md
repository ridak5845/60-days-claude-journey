
# 60 Days of Claude AI Challenge – Day 54

# Capstone Project — Day 4 of 10

## Core Feature Implementation
### One Verified Milestone at a Time

---

# Project

**CodeLens** — AI-Powered Multi-User Code Review Platform

---

# Objective

Today's goal was to complete one fully verified feature instead of partially implementing multiple features.

The milestone focused on building the complete authenticated workflow from repositories to pull request file review.

---

## Project Repo Link

https://github.com/ridak5845/codelens.git

# What Was Implemented

## Backend

### GitHub Service

Implemented new GitHub API integrations:

- Repository listing
- Pull Request listing
- Pull Request changed files

File:

```
server/services/githubService.js
```

---

### Repository Routes

Created protected API routes.

```
GET /api/repos

GET /api/repos/:owner/:repo/pulls

GET /api/repos/:owner/:repo/pulls/:number/files
```

File:

```
server/routes/repos.routes.js
```

---

### Authentication

Protected every endpoint using

```
requireAuth
```

Only authenticated GitHub users can access repository data.

---

### Server

Mounted the new routes inside

```
server.js
```

---

# Frontend

Built three new pages.

## Repositories

```
Repos.jsx
```

Displays authenticated user's GitHub repositories.

---

## Pull Requests

```
PullRequests.jsx
```

Displays open pull requests for the selected repository.

---

## PR Review

```
PRReview.jsx
```

Displays changed files for the selected pull request.

---

# Routing Chain

Successfully implemented complete navigation.

```
/dashboard

        ↓

/repos/:owner/:repo/pulls

        ↓

/repos/:owner/:repo/pulls/:number
```

---

# Verified Milestones

## ✅ Milestone 1

Authenticated API protection verified.

Unauthenticated requests correctly return

```
{
    "error": "Not authenticated"
}
```

---

## ✅ Milestone 2

Repository API successfully returns real GitHub repositories.

Verified endpoint:

```
GET /api/repos
```

Screenshot:


![GET](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/Screenshot%202026-07-24%20205700.png)


---

## ✅ Milestone 3

Repository Dashboard displays live repositories after login.

Verified:

- Repository cards
- Descriptions
- Updated timestamps

Screenshot:


![Dashboard](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/Screenshot%202026-07-24%20210416.png)


---

## ✅ Milestone 4

Navigation from repository to Pull Requests works.

Verified route:

```
/repos/:owner/:repo/pulls
```

Empty-state handling also confirmed using repositories without pull requests.

Screenshot:


![PULL](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/Screenshot%202026-07-24%20210445.png)


---

## ✅ Milestone 5

Tested against

```
facebook/react
```

Successfully fetched live Pull Requests from GitHub.

Screenshot:


![PUll](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/Screenshot%202026-07-24%20210858.png)


---

## ✅ Milestone 6

Changed Files page successfully displays modified files for a Pull Request.

Verified route

```
/repos/facebook/react/pulls/37110
```

Screenshot:


![pull](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/Screenshot%202026-07-24%20211158.png)


---

# Documentation Updated

**API.md**

[API.md](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/API%20(1).md)

**PROJECT-STRUCTURE.md**

[PROJECT-STRUCTURE](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/PROJECT-STRUCTURE%20(3).md)

Changes include:

- Added Pull Request Files endpoint
- Route naming clarification
- Authentication requirements
- Example API responses

---
# DAY4 SUMMARY

**SUMMARY.md**

[SUMMARY.md](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-54/DAY4-SUMMARY.md)

# Cleanup

Removed obsolete component

```
Dashboard.jsx
```

Benefits:

- Cleaner routing
- Less duplicate code
- Improved maintainability

---

# Biggest Debugging Challenge

The most difficult issue today was validating the complete authenticated workflow across multiple frontend pages and backend endpoints.

The workflow involved:

```
Authentication

↓

Repositories

↓

Pull Requests

↓

Changed Files
```

Every endpoint had to remain protected while correctly passing repository owner, repository name, and pull request number through the routing chain.

---

# How It Was Resolved

The workflow was verified incrementally.

1. Test every backend endpoint independently.
2. Verify authentication middleware.
3. Connect frontend pages one at a time.
4. Validate dynamic route parameters.
5. Test with personal repositories.
6. Test with a large public repository (facebook/react).

This isolated issues quickly and ensured every milestone worked before moving to the next.

---

# Tech Stack

Frontend

- React
- React Router
- Axios

Backend

- Node.js
- Express.js

Authentication

- GitHub OAuth

API

- GitHub REST API

---

# Key Learnings

- Finish one feature completely before starting another.
- Verify backend APIs independently before frontend integration.
- Protected routes should be tested with and without authentication.
- Dynamic routing becomes easier to debug when implemented incrementally.
- Documentation should evolve alongside the implementation.
- Testing against large real-world repositories helps uncover edge cases.

---

# Progress So Far

## Completed

- GitHub OAuth Authentication
- User Login
- Repository Dashboard
- Repository API
- Pull Request Listing
- Changed Files Viewer
- Protected Backend Routes
- Updated API Documentation

---

## Next Milestone

Implement AI-powered Pull Request Review.

Upcoming features:

- AI review generation
- Code quality suggestions
- Bug detection
- Security recommendations
- Review summaries
- Review history

---

# Deliverables

Included in today's update:

- ✅ DAY4-SUMMARY.md
- ✅ Updated API.md
- ✅ Verified milestone screenshots
- ✅ Backend implementation
- ✅ Frontend implementation
- ✅ Documentation updates
- ✅ Project committed and pushed to GitHub

---

# Reflection

Today's biggest takeaway was that shipping one fully verified milestone is far more valuable than building multiple unfinished features.

By completing the full repository → pull requests → changed files workflow, the project now has a solid foundation for integrating AI-powered code reviews in the next milestone.

---

**Day 54 Complete ✅**

**Project:** CodeLens – AI-Powered Multi-User Code Review Platform

**Challenge:** 60 Days of Claude AI Challenge
