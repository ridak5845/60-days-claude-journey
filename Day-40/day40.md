# Day 40 – Build Your Own AI Assistant 🤖

## 60 Days of Claude AI Challenge

## Project Name

**Skill Roadmap Architect**


# Project Overview

Skill Roadmap Architect is an AI-powered assistant that creates personalized learning roadmaps based on a user's current skill level, available study time, learning goals, and background.

Unlike generic roadmaps, it generates a structured, phase-by-phase learning plan that is realistic and actionable.


# Screenshots

## Home Screen

![Home Screen](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-40/hhome.png)


## Generated AI Roadmap

![Generated Roadmap](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-40/Screenshot%202026-07-10%20151834.png)


# Generated HTML Application

[HTML FILE](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-40/skill-roadmap-architect.html)

The application is built as a responsive HTML web app that allows users to:

- Enter the skill they want to learn
- Select current experience level
- Set weekly study hours
- Choose learning goals
- Add personal background
- Generate a personalized AI roadmap
- Download the roadmap as HTML
- Print or Save as PDF


# System Prompt

```text
Role & Scope
The system prompt casts Claude as a blunt, expert learning-roadmap mentor — someone who has actually hired, taught, and reviewed portfolios in the target field, not a generic encourager. It's explicitly scoped to one job: turn a skill goal + constraints into a realistic, phased learning plan. It's told to refuse scope creep (e.g. writing actual code, doing homework, or giving unrelated career advice) and redirect back to roadmap planning.

Structured Output Contract
Rather than freeform prose, the prompt requires strict JSON matching a schema (verdict score, phases with weeks/goals/tasks/resources/project, risks, a table of milestones, and a closing mentor note). This is what lets the frontend render a proper "document" instead of a chat bubble, and is what makes the file feel like a generated deliverable rather than a chat reply.

Realism Constraints
The prompt forces the model to do arithmetic sanity-checks against stated hours/week and deadline — if the timeframe is unrealistic for the stated goal, it must say so plainly in the verdict and adjust scope down rather than silently produce an undeliverable plan. This directly reflects the "blunt, expert mentor" tone: no padding, no vague encouragement, real tradeoffs named.

UI Decisions
Form + free text combo — structured fields (skill, level, hours, deadline, goal chips) capture the load-bearing constraints reliably, while the open text box catches nuance a form can't (e.g. "I quit twice before").
Document-style output — dark, editorial layout with a verdict box, phase cards, and a risk table so the roadmap reads like something worth saving, not a chat reply.
Download / Print actions — the roadmap can be saved as a standalone HTML file or printed to PDF directly from the browser, since the brief called for a "generated document" output.
Empty / loading / error states — handled distinctly so a network hiccup never looks like a blank or broken app.
Tone Calibration
"Blunt, expert mentor" is enforced at the prompt level with explicit instructions: no motivational filler, name the hardest part of each phase, call out when a deadline is unrealistic, and never inflate the verdict score to be encouraging.

Future Extensions
Tools — a web_search tool could pull current course/certification names and salary data instead of relying on the model's static knowledge of "best resources."
Memory — persisting past roadmaps (via window.storage) would let the assistant check in on progress, adjust the plan, and track which weeks were actually completed.
Multi-step workflow — a follow-up "week 1 check-in" mode that takes what the learner actually accomplished and re-forecasts the rest of the roadmap, rather than a single one-shot plan.
MCP integration — connecting a calendar tool to actually schedule the weekly blocks, or a task tool (e.g. Notion/Asana) to push the phase checklist directly into the learner's existing system.
```


# Features

- Personalized AI-generated roadmap
- Beginner to Advanced planning
- Weekly milestone generation
- Goal-oriented learning paths
- Download roadmap as HTML
- Print / Save as PDF
- Modern responsive UI
- Realistic study planning


# Technologies Used

- HTML5
- CSS3
- JavaScript
- Claude AI
- Prompt Engineering


# Key Learnings

- Learned how to design an AI assistant around a real user problem.
- Improved prompt engineering skills for structured AI outputs.
- Understood how system prompts influence response quality and consistency.
- Practiced building an interactive AI-powered web application.
- Learned to generate personalized learning plans instead of generic recommendations.
- Improved UI/UX design for AI applications.
- Gained experience in creating production-ready AI workflows.


# Outcome

The Skill Roadmap Architect successfully transforms user inputs into structured, realistic, and personalized learning plans that help learners stay focused, organized, and motivated throughout their learning journey.


## Challenge

**Day 40 of #60DayClaudeChallenge**

**Task:** Build Your Own AI Assistant

**Status:** ✅ Completed
