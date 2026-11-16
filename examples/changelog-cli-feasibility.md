# Changelog-generating CLI — feasibility review

> **Verdict**: Don't build it (as a commercial product) — generating a changelog from commit history is already covered by a free tool with 12.4k stars, a built-in GitHub feature, a Copilot-based action from GitHub, and a Claude Code command that is a single Markdown file. At least nine others have built the same thing, and what could be found of them is archived, deleted or ignored. The research turned up no evidence of anyone paying for the generation step on its own.
>
> **Assumptions** (this was an example run, so the clarifying questions were skipped):
> - The goal is a **commercial product with revenue**, aimed at indie developers, not a tool for personal use
> - Expected investment is a few months of spare time
> - No hard constraints on the stack
>
> If this is really a personal tool or a learning project, the verdict changes; see the last section.

## The idea, restated

- **Who, in what situation, hits what problem**: an indie developer shipping a release has to turn a pile of commits into notes a user can read. It interrupts the work and it is tedious.
- **How they cope today**: generate from Conventional Commits with git-cliff or release-please; click GitHub's "Generate release notes" button; ask Claude Code or Copilot to write it; or just write "bug fixes and improvements".
- **Why this idea is better**: **the original pitch does not say.** The following is my reading, not your claim: "use an LLM to rewrite developer-facing commits into user-facing language, without requiring a commit convention". Research tested that reading and it **does not hold**. changelogai's pitch is exactly "no Conventional Commits needed, plus AI rewriting into user-facing notes", and GitHub's copilot-release-notes works the same way. This line cannot currently be filled in with anything existing tools lack, and that is the direct reason for the verdict.
- **Delivery form**: CLI.

## What already exists

**Free options already cover everything this idea does.**

| Name | Type | Price | Status | Source |
|---|---|---|---|---|
| git-cliff | Open-source CLI (Rust); generates from Conventional Commits or custom regex | Free | 12.4k stars, 1,615 commits; no AI features | [GitHub](https://github.com/orhun/git-cliff) |
| release-please / Changesets | Open source; release-PR workflow / JS monorepos | Free | The default recommendations in several 2026 comparison articles | [Comparison](https://www.pistack.xyz/posts/2026-04-22-git-cliff-vs-release-please-vs-auto-self-hosted-changelog-generator-guide-2026/) |
| GitHub auto-generated release notes | Built-in button; categories configurable in `.github/release.yml` | Free | Long-maintained official docs; still being updated in June 2026 | [GitHub Docs](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes), [GitHub Changelog](https://github.blog/changelog/2026-06-18-generated-release-notes-credit-you-for-copilot-pull-requests) |
| github/copilot-release-notes | An Action under the GitHub organisation; uses Copilot CLI to read PR titles, bodies and diffs | Requires a Copilot licence | 24 stars; maintained by the gh-cli-and-desktop team | [GitHub](https://github.com/github/copilot-release-notes) |
| `/changelog`-style commands and skills for Claude Code | A single Markdown file | Free (uses an existing subscription) | At least 6 community versions found; command-changelog has 1 star | [command-changelog](https://github.com/claude-commands/command-changelog), [heyclau.de](https://heyclau.de/entry/commands/release-notes-drafting) |
| AI changelog CLIs: kacs, llm-git, ai_changelog, changelogai, aichangelog, logedit, changeish, clgen | Open-source CLIs, nearly identical to this idea | Free | Eight surfaced in a single search; of those checked, kacs has **0 stars** and clgen has 0 | [kacs](https://github.com/atasoglu/kacs), [clgen](https://github.com/CMXX648/clgen), [changeish](https://dev.to/itlackey/changeish-automate-your-changelog-with-ai-45kj) |

**The paid products sell delivery, not generation.**

| Name | Price | What it sells | Source |
|---|---|---|---|
| Beamer | Free tier (1,000 MAU); $49 / $99 / $249 per month | In-app widget plus a standalone page | [Third-party review](https://www.productlift.dev/blog/beamer-pricing/) (not the official pricing page) |
| Headway | $29 per month | Updates page plus widget | [dev.to](https://dev.to/kavinjeya/the-state-of-changelog-tools-for-indie-saas-in-2026-2f22) (the author builds the competing Shiplog) |
| AnnounceKit | $79–129 per month | Widget, email, segmentation | Same |
| Shiplog | $19 per month | GitHub PR sync, AI generation, widget, email | Same |

All of these prices come from third-party articles or a competitor's own post. They were not checked against each official pricing page and are only good for order of magnitude.

**The graveyard**: two rounds of searching for comparable products that shut down or pivoted found no formal shutdown notice or postmortem. The nearest thing was a directory site saying ReleaseNote AI "may no longer be operational as of early 2026", which is a third-party inference and unverified. Small tools rarely announce their end, so the search moved to comparable projects that nobody noticed:

| Project | Current state | Source |
|---|---|---|
| ai_changelog (Python; LLM summaries of commits) | 14 stars, 365 commits, **archived by its owner on 2026-09-04** | [GitHub](https://github.com/joshuasundance-swca/ai_changelog) |
| aichangelog (Rust; OpenAI generation) | 1 star, 5 commits, **archived by its owner on 2025-08-02** | [GitHub](https://github.com/dikkadev/aichangelog) |
| logedit (Python; OpenAI generation) | **Repository URL returns 404**; deleted or made private | [GitHub](https://github.com/gregpriday/logedit) |
| kacs (Python; LLM categorisation) | 0 stars, 0 forks, 28 commits | [GitHub](https://github.com/atasoglu/kacs) |
| clgen (LLM generation for several audiences) | 0 stars | [GitHub](https://github.com/CMXX648/clgen) |
| command-changelog (Claude Code command) | 1 star, 0 forks | [GitHub](https://github.com/claude-commands/command-changelog) |
| changeish launch post (2025-06-17) | 1 comment, which is an ad for the competing Shiplog | [dev.to](https://dev.to/itlackey/changeish-automate-your-changelog-with-ai-45kj) |
| changelogai launch post (28 March) | 1 comment, the same Shiplog ad; the author gives no download or star numbers | [dev.to](https://dev.to/uratools/i-built-a-changelog-generator-that-works-on-any-repo-no-conventional-commits-needed-1ekc) |
| Proseflow launch post (26 April; free tool) | 0 comments, no usage numbers | [dev.to](https://dev.to/shiplogbot/show-hn-i-built-a-free-ai-tool-that-generates-changelogs-from-github-commits-3-tones-12o6) |

Of the nine, two are archived and one is gone, and the rest have at most one star. The three launch posts drew two replies in total, both ads from the same competitor. The only project with some attention is llm-git (62 stars, [GitHub](https://github.com/can1357/llm-git)), and its main feature is generating commit messages; the changelog is a side feature. **This is not missing data. It is a finding about demand: the thing has been built again and again, and it has not been noticed once.**

One more post on Indie Hackers, titled along the lines of "I'm building a tool to simplify changelog creation", returned 403 and could not be read. It is left out of the table.

**Demand-side signal**: an Ask HN thread with 38 points and about 50 comments, roughly 11 months old, is specifically about how people write release notes ([HN](https://hn.svelte.dev/item/46257486)):
- Several commenters recommend git-cliff, release-please and Changesets outright
- Several say nobody reads them ("No one read them", "Hardly anyone reads them")
- Some think an LLM can write the first draft, and each of them adds that a human has to review it
- **Not one comment says its author would pay for a tool**

A search of Chinese-language forums (V2EX and similar) in this round found no complaint threads. That is recorded as "no signal", not as proof that the demand is absent.

## Scorecard

Baseline: a commercial product for indie developers. 5 is the favourable end.

| Dimension | Score | Reasoning | Basis |
|---|---|---|---|
| Pain intensity | 2 | The tedium is real but it is a "nice to have"; in the discussion thread several people say nobody reads release notes and nobody offers to pay; comparable tools launch to silence | Fact: the HN thread and the graveyard table |
| Market size | 2 | No reliable public data on the number of indie developers; the reachable channels (HN, dev.to, Indie Hackers) are already full of launch posts for the same thing, and those posts get almost no response | Fact: launch posts with 0 comments; the headcount is judgement |
| Differentiation & moat | 1 | Free products already do this, one of them from the platform owner; the feature can be copied with a Markdown prompt file; the "why better" line cannot be filled in | Fact: see "What already exists" |
| Technical feasibility | 5 | Read the git log, call an LLM, write a file: all proven parts | Judgement, supported by eight existing implementations |
| Cost & effort | 5 | One person, a few weekends; running cost is close to zero (see below) | Judgement |
| Path to revenue | 1 | Under a cent per run leaves nothing to mark up; when users bring their own API key there is no step you can charge for; every comparable CLI is free | Fact: pricing; judgement: token usage |
| Risk | 3 | Sending private-repository diffs to a third-party model will put some users off; the platform is clearly moving toward building this in | Fact: VS Code's release notes are now generated by Copilot ([report](https://visualstudiomagazine.com/articles/2026/09/17/vs-code-release-notes-now-generated-by-copilot-might-contain-inaccuracies.aspx?p=1)) |

**Unit cost estimate**: Claude Haiku 5.5 is officially priced at $0.10 input and $0.50 output per million tokens ([Anthropic pricing](https://platform.claude.com/docs/en/about-claude/pricing)). Assuming a release of 50 commits takes about 20,000 input tokens and 1,000 output tokens (the token counts are my estimate), the cost is about $0.002 + $0.0005 = **$0.0025**. That is good for the user and bad for your pricing.

**How the scores lead to the verdict**: a dimension that applies scored 1, so "Build it" is ruled out. This is a commercial product and the 1s fall on Differentiation & moat and Path to revenue, which points to "Don't build it" or "Build something smaller". No smaller version gets around those two problems, because however small it is, it is still the same free thing. So the verdict is "Don't build it". The two 5s on feasibility and cost only say that it can be built.

## How this most likely dies

1. **No response on launch day.** You post on HN or dev.to and the first comment is "how is this different from git-cliff plus a prompt?", or there is no comment at all. Early sign: single-digit comments and stars a week after the launch post, which is where the nine projects in the graveyard table are today.
2. **The tool your users already have turns it into one sentence.** The target user, an indie developer comfortable with a CLI, very likely already uses Claude Code, Cursor or Copilot. Typing "write release notes from the commits since the last tag" gets the job done with nothing to install. Early sign: people who try it report "I just asked the AI".
3. **People use it and nobody pays.** Even with a few hundred users, a CLI that runs on the user's own API key has nowhere to charge. Charging means building a hosted page, a widget and email delivery. That is a different product, in a market where Shiplog already charges $19 a month. Early sign: zero conversions once a paywall goes up.

## Verdict and next step

**Don't build it** as a commercial product. The reason is not that it can't be built. Once built, it would be no different from the free options and would have nowhere to charge.

**An adjacent direction worth more**: the only step in the research that people pay for is getting updates in front of end users (in-app widgets, email), not generating the text. That direction also has several products already, starting at $19 a month, so it is better than the original idea but not yet worth building. Taking it would need a fresh review first, centred on whether you can find a specific group the existing products don't serve. This paragraph is my judgement; no separate research was done for it.

**What to do this week**:
- If the release notes simply annoy you when you ship: install git-cliff, or drop a `/changelog` command file into Claude Code. Ten minutes, no project needed.
- If you still want to test the commercial case: write no code yet. Find ten indie developers who have shipped a release and ask how they wrote their last release notes, how long it took, and whether they have ever paid for help with it. If three or more have paid or would pay, come back and review it again.

**If the assumptions are wrong**: for a personal tool or a learning project, Market size and Path to revenue become "n/a" and the verdict becomes **Build it**. It is small, technically certain and doable in a weekend, which makes it a good practice project.

## Sources

All accessed on 2026-11-16.

- git-cliff repository: https://github.com/orhun/git-cliff
- git-cliff / release-please / auto comparison (2026-04-22): https://www.pistack.xyz/posts/2026-04-22-git-cliff-vs-release-please-vs-auto-self-hosted-changelog-generator-guide-2026/
- GitHub automatically generated release notes docs: https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes
- ai_changelog (archived): https://github.com/joshuasundance-swca/ai_changelog
- aichangelog (archived): https://github.com/dikkadev/aichangelog
- logedit (404): https://github.com/gregpriday/logedit
- llm-git: https://github.com/can1357/llm-git
- changelogai launch post (28 March; the page shows no year): https://dev.to/uratools/i-built-a-changelog-generator-that-works-on-any-repo-no-conventional-commits-needed-1ekc
- GitHub Changelog (2026-06-18): https://github.blog/changelog/2026-06-18-generated-release-notes-credit-you-for-copilot-pull-requests
- github/copilot-release-notes: https://github.com/github/copilot-release-notes
- VS Code release notes generated by Copilot (2026-09-17): https://visualstudiomagazine.com/articles/2026/09/17/vs-code-release-notes-now-generated-by-copilot-might-contain-inaccuracies.aspx?p=1
- command-changelog (Claude Code command): https://github.com/claude-commands/command-changelog
- /draft-release-notes command: https://heyclau.de/entry/commands/release-notes-drafting
- kacs: https://github.com/atasoglu/kacs
- clgen: https://github.com/CMXX648/clgen
- changeish: https://dev.to/itlackey/changeish-automate-your-changelog-with-ai-45kj
- Free AI changelog tool launch post (26 April; the page shows no year): https://dev.to/shiplogbot/show-hn-i-built-a-free-ai-tool-that-generates-changelogs-from-github-commits-3-tones-12o6
- The state of changelog tools for indie SaaS (5 July; written by Shiplog's developer): https://dev.to/kavinjeya/the-state-of-changelog-tools-for-indie-saas-in-2026-2f22
- Beamer pricing (third party): https://www.productlift.dev/blog/beamer-pricing/
- Ask HN on handling release notes: https://hn.svelte.dev/item/46257486
- Anthropic official pricing: https://platform.claude.com/docs/en/about-claude/pricing
