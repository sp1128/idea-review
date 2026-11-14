---
name: idea-review
description: Turn a raw product idea into an honest verdict and a build plan. Use whenever someone pitches a product, app, tool or business idea, says "I want to build X", asks "is this worth doing?" or "has someone already built this?", or wants an idea validated before starting — even if they never use the word "evaluate". Researches the market first, scores the idea across seven dimensions, then writes a task list a coding agent can execute. Not for implementation help on a project that is already under way.
---

# Idea Review

Turn a vague idea into two things: an honest **verdict** (should this be built, and why) and an executable **task list** (if yes, how).

## Rule zero: do not be a cheerleader

People arrive attached to their own ideas and expecting validation. The default response drifts toward flattery — and flattery makes this skill worthless.

- Every report **must** include a "How this most likely dies" section with concrete failure paths, not vague hand-waving about "a competitive market".
- If research turns up a mature free or open-source alternative, **say so in the opening paragraph**, not buried in section three.
- "Don't build this" and "build this much smaller thing instead" are valid verdicts. Deliver them without apologising and without cushioning.
- Separate **facts** (with sources) from **judgement** (yours). Market numbers need a link; if you can't find one, write "no reliable public data" — never invent a figure.
- The same honesty cuts the other way: don't manufacture objections to look rigorous. If the research is favourable, say "build it" plainly.

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

If you can't fill in any one of these lines, the idea isn't formed yet. Stop here: tell the user which line is missing and ask for it, instead of forcing out a report built on a guess. When running unattended, pick the most plausible reading, flag it as an assumption at the top, and continue.

### 3. Research (do not skip)

**Research before judging.** Fire parallel searches in one batch rather than serially. Cover at least:

- **Direct competitors**: name, pricing, user base or reviews. Search in English *and* in the user's local-market language — the answers often differ.
- **The graveyard**: who tried this and shut down or pivoted, and why. A dead competitor tells you more than a live one.
- **Open-source alternatives**: what already exists on GitHub (stars, last commit, activity).
- **State of the enabling tech**: is the hard part already a mature library or API, and what does it cost? For metered APIs, work out the per-unit cost and compare it with what a user would plausibly pay.
- **Demand-side signal**: is anyone on Reddit, Hacker News, or the relevant local forums complaining about this problem? Complaints with workarounds are strong signal; silence is signal too.
- **Distribution**: where these users gather, and how the existing players acquired theirs. "Build it and they'll come" is not a channel.
- **Regulatory tripwires**: mandatory when payments, health, education, scraping, user-generated content, or personal data are involved.

Use WebFetch to read the key pages, not just search snippets. Note the date of anything you cite — pricing and market figures older than about two years get flagged as stale.

Research with a **falsifying mindset**: actively search "why did X fail", "X alternative" — not only material that supports the conclusion you already like.

Stop when new searches stop changing the picture; that is usually after 8–15 of them. If no search tool is available, say so in the first line of the report, label every market and competitor claim as unverified recollection, and give no figures.

### 4. Scorecard

Seven dimensions, 1–5 each, with one line of reasoning and its basis (source, or explicitly marked as judgement). **5 is always the favourable end** — for Cost & effort and Risk, a 5 means cheap and safe.

| Dimension | What you're looking at | A 1 looks like | A 5 looks like |
|---|---|---|---|
| Pain intensity | "Can't stand it" vs "nice to have". Has anyone paid to solve it? | Nobody complains about it | People pay for bad workarounds |
| Market size | Reachable user count, and how the first 100 are reached. Niche is fine, but name the ceiling. | A few hundred people, no channel | Large and reachable through a known channel |
| Differentiation & moat | Could an incumbent clone it in a week? Is the moat data, distribution, UX, or cost? | A free incumbent already does it | Hard to copy, and you can say why |
| Technical feasibility | What's the most uncertain part, and is there a proven fallback? | Depends on an unsolved problem | Assembly of proven parts |
| Cost & effort | Person-months, plus recurring cost (hosting, APIs, moderation). | Far beyond the stated budget | Fits the budget with margin |
| Path to revenue | Who pays, how much, when. Free tools still need a reason the effort is worth it. | No one would pay, or unit cost exceeds price | Comparable products charge today |
| Risk | Compliance, platform dependency (API cutoff, store removal), legal, single points of failure. | One outside decision kills it | Nothing outside your control |

Score against the baseline from step 1. For a personal tool or a learning project, mark Market size and Path to revenue "n/a" rather than punishing the idea for not being a business; for a commercial product those two carry the verdict.

Then the **verdict**, exactly one of:

- **Build it** — proceed to the technical plan
- **Build it if** — one fatal assumption must be validated first; give the validation method and the bar (how long, how much, what result counts as a pass)
- **Build something smaller** — the idea is too big; name the smaller thing to do first
- **Don't build it** — give the reason, and one adjacent direction worth more

The verdict has to follow from the scorecard, and scores are not averaged: one fatal dimension outweighs four good ones. A 1 on pain, feasibility or risk rules out a plain "Build it".

### 5. Technical plan

Only when the verdict isn't "don't build it". Plan the thing the verdict points to: for "build something smaller", plan the smaller thing; for "build it if", keep this to a one-page sketch, since detailed design is wasted until the assumption holds.

- **Architecture sketch**: a Mermaid diagram of components or data flow — not just prose
- **Stack choices**: for each key decision, one recommendation, one alternative, and the reason. Favour the stack the user already knows.
- **Data model**: core entities and relationships. Enough to be useful; not full DDL.
- **Hard parts**: at most three, each with a mitigation *and* a downgrade path if the mitigation fails
- **MVP boundary**: explicitly list **what v1 does not do**. This matters more than the feature list.
- **Milestones**: 3–4 stages, each with a verifiable output and a rough duration

### 6. Development task list

A separate file, written for a coding agent rather than a human reader. That agent has not read the report, so open the file with a short context block: what the product is in one paragraph, the chosen stack, the "not in v1" list, and how to run and test the project.

For "build it if", the list contains only the validation experiment — often a spike, a landing page or ten user conversations rather than product code — and ends at the pass/fail bar. The full build gets tasked out after the result is in.

Each task:

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
- Schedule the riskiest hard part from step 5 as early as its dependencies allow, so a dead end shows up in week one and not in week six

## Output

Two Markdown files, named after a short slug for the idea:

1. `<slug>-feasibility.md`
2. `<slug>-tasks.md` (skip when the verdict is "don't build it")

Write both in the language the user pitched the idea in. The feasibility report follows this order, so the reader meets the conclusion before the evidence:

```
# <Idea name> — feasibility review
> **Verdict**: <one of the four> — <one sentence why>
> **Assumptions**: <only if step 1 was skipped>

## The idea, restated          four lines from step 2
## What already exists         competitors, graveyard, open source — a table with links
## Scorecard                   seven rows: score, reasoning, basis
## How this most likely dies   2–3 failure paths, each with the early sign that it is happening
## Verdict and next step       what to do this week
## Technical plan              omitted for "don't build it"
## Sources                     every link cited, with access date
```

Write them to the session's output directory and deliver them as files. If the user has a local folder connected, write them there too.

Keep the chat reply to two or three sentences: the verdict, plus the single most important risk. Don't restate the report — the user will open it.

## After delivery

Users often argue with a verdict they dislike. Change it for new facts or new constraints ("I already have 200 people on a waitlist", "it only needs to work for my team"), and update the files when you do. Do not change it for enthusiasm or repetition; say which evidence would change your mind instead.

## Common failure modes

Re-read the report against this list before delivering it.

- **Skipping research and writing from memory** — the most common and the most damaging. Market and competitor claims must come from this session's searches.
- **Writing a requirements doc instead of an evaluation** — the point of the report is the *should we* judgement, not a feature inventory.
- **A verdict the scorecard doesn't support** — "Build it" above a row of 2s means one of the two is wrong.
- **Mismatching the reader's level** — don't explain REST to a senior engineer; don't open with Kubernetes for a beginner.
- **An MVP that isn't small** — if the v1 list exceeds 15 tasks, cut again.
- **Every idea scores a 4** — a scorecard with no spread is noise. Give a 2 when a 2 is right.
- **Failure paths that fit any startup** — "runs out of money" and "competition is fierce" say nothing. Each path should name something specific to this idea.
