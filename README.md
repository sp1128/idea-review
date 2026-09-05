# idea-review

A Claude skill that takes a raw product idea and gives you back an **honest verdict** and an **executable build plan**.

[中文文档](README.zh-CN.md)

---

## Why

Ask any AI assistant to evaluate your idea and you'll get a wall of encouragement. It will find a market, invent a TAM, list ten features, and tell you it has strong potential. This is the single least useful thing an assistant can do at the moment you're deciding whether to spend three months of your life on something.

`idea-review` is built around the opposite default. It is explicitly instructed to:

- **Research before judging.** Market claims, competitor names and pricing must come from actual searches performed in that session — never from the model's memory.
- **Search for disconfirming evidence.** It deliberately looks for "why did X fail" and "X alternative", not only material that supports a flattering conclusion.
- **Lead with the bad news.** If a mature free or open-source alternative already exists, that goes in the opening paragraph, not buried in section three.
- **Be willing to say no.** "Don't build this" and "build this much smaller thing instead" are first-class verdicts, delivered without apology or cushioning.
- **Separate fact from judgement.** Every market number carries a source link. When there isn't one, it says "no reliable public data" instead of inventing a figure.

## What you get

Two Markdown files:

| File | Contents |
|---|---|
| `<slug>-feasibility.md` | Restated idea, research findings, a 7-dimension scorecard, a one-sentence verdict, how this most likely dies, and — if the verdict isn't "no" — the technical plan |
| `<slug>-tasks.md` | Dependency-ordered tasks with machine-checkable acceptance criteria, sized half a day to two days each, ready to hand to a coding agent |

## Install

**Claude Code** — personal skill (available in every project):

```bash
git clone https://github.com/sp1128/idea-review.git ~/.claude/skills/idea-review
```

Or scoped to one project:

```bash
git clone https://github.com/sp1128/idea-review.git .claude/skills/idea-review
```

**Claude (Cowork / desktop)** — open `SKILL.md`, copy its contents, and ask Claude to save it as a skill.

Verify it loaded with `/skills` in Claude Code.

## Usage

Just describe the idea. The skill triggers on its own:

```
I want to build a tool that turns podcast episodes into searchable transcripts with speaker labels
```

```
Is it worth building a Chrome extension that de-duplicates job listings across boards?
```

```
我想做一个给独立开发者用的、自动生成变更日志的 CLI
```

It will ask at most one round of clarifying questions — who it's for, how much time you're willing to spend, any hard constraints — then research, score, and write.

## How it works

```
Clarify (≤3 questions)
   ↓
Restate the idea in 4 lines  ← if you can't fill these in, the idea isn't formed yet
   ↓
Parallel research  ← competitors · open-source alternatives · enabling tech & unit cost
   ↓                  demand signal · regulatory tripwires
Scorecard (7 dimensions, 1–5)
   ↓
Verdict: build it / build it if / build something smaller / don't build it
   ↓
Technical plan (architecture, stack, hard parts, MVP boundary, milestones)
   ↓
Task list for a coding agent
```

### The scorecard

| Dimension | What it's looking at |
|---|---|
| Pain intensity | "Can't stand it" vs "nice to have". Has anyone paid to solve it? |
| Market size | Reachable user count. Niche is fine — but name the ceiling. |
| Differentiation & moat | Could an incumbent clone it in a week? |
| Technical feasibility | What's the most uncertain part, and is there a proven fallback? |
| Cost & effort | Person-months, plus recurring cost (hosting, APIs, moderation). |
| Path to revenue | Who pays, how much, when. |
| Risk | Compliance, platform dependency, legal, single points of failure. |

A scorecard where everything lands on 4 is noise. The skill is instructed to give a 2 when a 2 is right.

### What a task looks like

```markdown
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

Acceptance criteria must be machine-checkable or click-checkable. "Delivers a good user experience" is not an acceptance criterion.

## Language

`SKILL.md` is in English. A Chinese version lives in [`i18n/SKILL.zh-CN.md`](i18n/SKILL.zh-CN.md) — the skill's instructions are what shapes the report's language, so to get Chinese output, install that file as `SKILL.md` instead:

```bash
cp i18n/SKILL.zh-CN.md SKILL.md
```

## Customising

The skill is a single Markdown file — edit it directly. Common adjustments:

- **Add a dimension** to the scorecard (regulatory burden, hiring difficulty, ecosystem lock-in)
- **Change the research sources** — swap Reddit and Hacker News for the forums your market actually lives on
- **Pin your stack** — add "prefer Go + Postgres" so the technical plan stops proposing a new language every time
- **Tighten the MVP ceiling** — the default cuts at 15 tasks; lower it if your v1s keep sprawling

## Contributing

Issues and PRs welcome. The most valuable contributions are **cases where the skill got it wrong** — an idea it talked up that shouldn't have been built, or a research step it skipped. Paste the idea and the output it produced; that's what makes the instructions better.

## License

MIT — see [LICENSE](LICENSE).
