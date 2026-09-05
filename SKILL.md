---
name: idea-review
description: Turn a raw product idea into an honest verdict and a build plan. Use when someone pitches an idea, says "I want to build X", or asks whether an idea is worth doing. Researches the market first, scores the idea across seven dimensions, then writes a task list a coding agent can execute.
---

# Idea Review

Turn a vague idea into two things: an honest **verdict** (should this be built, and why) and an executable **task list** (if yes, how).

## Rule zero: do not be a cheerleader

People arrive attached to their own ideas and expecting validation. The default response drifts toward flattery — and flattery makes this skill worthless.

- Every report **must** include a "How this most likely dies" section with concrete failure paths, not vague hand-waving about "a competitive market".
- If research turns up a mature free or open-source alternative, **say so in the opening paragraph**, not buried in section three.
- "Don't build this" and "build this much smaller thing instead" are valid verdicts. Deliver them without apologising and without cushioning.
- Separate **facts** (with sources) from **judgement** (yours). Market numbers need a link; if you can't find one, write "no reliable public data" — never invent a figure.

## Process

### 1. Clarify (when someone is there to answer)

Ask at most one round of 2–3 questions. Only ask what would **change the verdict**:

- Who is this for — yourself, a niche community, or a commercial product? This sets the entire evaluation baseline.
- How much investment is expected — a weekend, a few months, full-time?
- Any hard constraints — required stack, self-hosting, budget ceiling, deadline?

Skip this when the user already covered it, or when running unattended (a scheduled run, or the user said "just go"). In that case state your assumptions at the top of the report.

### 2. Restate the idea

Break it into four lines so the user can immediately see whether you understood it:

- **Who**, in **what situation**, hits **what problem**
- How they **cope today**
- Why this idea is **better**
- Delivery form (web / app / CLI / plugin / API / hardware)

If you can't fill in any one of these lines, the idea isn't formed yet. Say that instead of forcing out a report.

### 3. Research (do not skip)

**Research before judging.** Fire parallel searches in one batch rather than serially. Cover at least:

- **Direct competitors**: name, pricing, user base or reviews. Search in English *and* in the user's local-market language — the answers often differ.
- **Open-source alternatives**: what already exists on GitHub (stars, last commit, activity).
- **State of the enabling tech**: is the hard part already a mature library or API, and what does it cost? For metered APIs, work out the per-unit cost.
- **Demand-side signal**: is anyone on Reddit, Hacker News, or the relevant local forums complaining about this problem?
- **Regulatory tripwires**: mandatory when payments, health, education, scraping, user-generated content, or personal data are involved.

Use WebFetch to read the key pages, not just search snippets.

Research with a **falsifying mindset**: actively search "why did X fail", "X alternative" — not only material that supports the conclusion you already like.

### 4. Scorecard

Seven dimensions, 1–5 each, with one line of reasoning and its basis (source, or explicitly marked as judgement):

| Dimension | What you're looking at |
|---|---|
| Pain intensity | "Can't stand it" vs "nice to have". Has anyone paid to solve it? |
| Market size | Reachable user count. Niche is fine, but name the ceiling. |
| Differentiation & moat | Could an incumbent clone it in a week? Is the moat data, distribution, UX, or cost? |
| Technical feasibility | What's the most uncertain part, and is there a proven fallback? |
| Cost & effort | Person-months, plus recurring cost (hosting, APIs, moderation). |
| Path to revenue | Who pays, how much, when. Free tools still need a reason the effort is worth it. |
| Risk | Compliance, platform dependency (API cutoff, store removal), legal, single points of failure. |

Then one sentence of **verdict**, exactly one of:

- **Build it** — proceed to the technical plan
- **Build it if** — one fatal assumption must be validated first; give the validation method and the bar (how long, how much, what result counts as a pass)
- **Build something smaller** — the idea is too big; name the smaller thing to do first
- **Don't build it** — give the reason, and one adjacent direction worth more

### 5. Technical plan

Only when the verdict isn't "don't build it". Include:

- **Architecture sketch**: a Mermaid diagram of components or data flow — not just prose
- **Stack choices**: for each key decision, one recommendation, one alternative, and the reason. Favour the stack the user already knows.
- **Data model**: core entities and relationships. Enough to be useful; not full DDL.
- **Hard parts**: at most three, each with a mitigation *and* a downgrade path if the mitigation fails
- **MVP boundary**: explicitly list **what v1 does not do**. This matters more than the feature list.
- **Milestones**: 3–4 stages, each with a verifiable output and a rough duration

### 6. Development task list

A separate file, written for a coding agent rather than a human reader. Each task:

```
### T-03 User authentication
**Goal**: email signup/login with persistent sessions
**Acceptance criteria**:
- [ ] Visiting /dashboard while logged out redirects to /login
- [ ] Signup logs the user in; the session survives a page refresh
- [ ] A wrong password returns 401 without revealing whether the account exists
**Depends on**: T-01 (scaffold), T-02 (database schema)
**Touches**: src/auth/, middleware/session.ts
**Estimate**: 0.5 day
```

Requirements:

- Order by dependency; mark which tasks can run in parallel
- Acceptance criteria must be **machine-checkable or click-checkable** behaviour, never "delivers a good user experience"
- Keep tasks between half a day and two days; split anything larger
- The first task is always the smallest skeleton that runs — an end-to-end hello world

## Output

Two Markdown files, named after a short slug for the idea:

1. `<slug>-feasibility.md`
2. `<slug>-tasks.md` (skip when the verdict is "don't build it")

Write them to the session's output directory and deliver them as files. If the user has a local folder connected, write them there too.

Keep the chat reply to two or three sentences: the verdict, plus the single most important risk. Don't restate the report — the user will open it.

## Common failure modes

- **Skipping research and writing from memory** — the most common and the most damaging. Market and competitor claims must come from this session's searches.
- **Writing a requirements doc instead of an evaluation** — the point of the report is the *should we* judgement, not a feature inventory.
- **Mismatching the reader's level** — don't explain REST to a senior engineer; don't open with Kubernetes for a beginner.
- **An MVP that isn't small** — if the v1 list exceeds 15 tasks, cut again.
- **Every idea scores a 4** — a scorecard with no spread is noise. Give a 2 when a 2 is right.
