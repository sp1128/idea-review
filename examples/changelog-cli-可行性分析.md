# 自动生成变更日志的 CLI — 可行性分析

> **结论**：不建议做（作为商业产品）— "从提交记录生成变更日志"这件事已经有 12.4k star 的免费工具、GitHub 自带功能、GitHub 官方的 Copilot 版本，以及一个 Markdown 文件就能实现的 Claude Code 命令；做过同样东西的人至少有 9 批，查到的结果是归档、删库或零回应；调研中找不到任何人为"生成"这一步单独付费的证据。
>
> **假设**（本次为示例运行，跳过了澄清提问）：
> - 目标是做成一个**有收入的商业产品**，面向独立开发者，而不是只给自己用
> - 投入预期为业余时间的几个月
> - 没有技术栈硬约束
>
> 如果真实情况是"自己用 / 练手"，结论会不同，见最后一节。

## 想法重述

- **谁、什么场景、什么问题**：独立开发者每次发版时，要从一堆提交记录里整理出一份给用户看的更新说明，这件事打断开发节奏，而且枯燥。
- **现在怎么凑合**：用 git-cliff / release-please 按 Conventional Commits 自动生成；点 GitHub 的"Generate release notes"按钮；让 Claude Code / Copilot 直接写；或者干脆写一句"修复了一些问题"。
- **凭什么更好**：**原始想法里没有这一行。** 下面这句是我的理解，不是你的主张："用 LLM 把面向开发者的提交记录改写成面向用户的语言，且不要求规范的提交格式"。调研对它的检验结果是**不成立**——changelogai 的卖点一字不差就是"不需要 Conventional Commits + AI 改写成面向用户的说明"，GitHub 的 copilot-release-notes 也是同样做法。也就是说，这一行目前填不出一个现有工具没有的答案，这是本报告结论的直接原因。
- **交付形态**：CLI。

## 已经有什么

**免费方案已经覆盖了这个想法的全部功能。**

| 名称 | 类型 | 价格 | 状况 | 来源 |
|---|---|---|---|---|
| git-cliff | 开源 CLI（Rust），按 Conventional Commits 或自定义正则生成 | 免费 | 12.4k star，1,615 次提交；无 AI 功能 | [GitHub](https://github.com/orhun/git-cliff) |
| release-please / Changesets | 开源，发版 PR 流程 / JS monorepo | 免费 | 多篇 2026 年对比文章中的默认推荐 | [对比文章](https://www.pistack.xyz/posts/2026-04-22-git-cliff-vs-release-please-vs-auto-self-hosted-changelog-generator-guide-2026/) |
| GitHub 自动生成 release notes | 平台内置按钮，可用 `.github/release.yml` 配置分类 | 免费 | 官方文档长期维护；2026-06 仍在更新 | [GitHub Docs](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes)、[GitHub Changelog](https://github.blog/changelog/2026-06-18-generated-release-notes-credit-you-for-copilot-pull-requests) |
| github/copilot-release-notes | GitHub 组织下的 Action，用 Copilot CLI 读 PR 标题、正文、diff 生成 | 需要 Copilot 订阅 | 24 star，由 gh-cli-and-desktop 团队维护 | [GitHub](https://github.com/github/copilot-release-notes) |
| Claude Code 的 `/changelog` 类命令和 skill | 一个 Markdown 文件 | 免费（用已有订阅） | 搜到至少 6 个社区版本；其中 command-changelog 只有 1 star | [command-changelog](https://github.com/claude-commands/command-changelog)、[heyclau.de](https://heyclau.de/entry/commands/release-notes-drafting) |
| AI 变更日志 CLI：kacs、llm-git、ai_changelog、changelogai、aichangelog、logedit、changeish、clgen | 开源 CLI，和本想法几乎一致 | 免费 | 一次搜索就出现 8 个；抽查的 kacs 为 **0 star**、clgen 为 0 star | [kacs](https://github.com/atasoglu/kacs)、[clgen](https://github.com/CMXX648/clgen)、[changeish](https://dev.to/itlackey/changeish-automate-your-changelog-with-ai-45kj) |

**收费的产品卖的不是"生成"，而是"送达"。**

| 名称 | 价格 | 卖的是什么 | 来源 |
|---|---|---|---|
| Beamer | 免费档（1,000 MAU）；$49 / $99 / $249 每月 | 应用内小组件 + 独立页面 | [第三方评测](https://www.productlift.dev/blog/beamer-pricing/)（非官方定价页） |
| Headway | $29 每月 | 更新页面 + 小组件 | [dev.to](https://dev.to/kavinjeya/the-state-of-changelog-tools-for-indie-saas-in-2026-2f22)（作者是竞品 Shiplog 的开发者） |
| AnnounceKit | $79–129 每月 | 小组件、邮件、分群 | 同上 |
| Shiplog | $19 每月 | GitHub PR 同步 + AI 生成 + 小组件 + 邮件 | 同上 |

以上定价均来自第三方或竞品作者的文章，没有逐一核对官方定价页，仅用于判断量级。

**墓地**：搜了两轮关停或转型的同类产品，没有找到任何正式的关停公告或复盘（唯一接近的是一个目录站称 ReleaseNote AI "2026 年初可能已停止运营"，属第三方推断，未核实）。小工具通常不发公告，所以改查无人问津的同类项目，结果如下：

| 项目 | 现状 | 来源 |
|---|---|---|
| ai_changelog（Python，LLM 总结提交） | 14 star，365 次提交，**2026-09-04 被作者归档** | [GitHub](https://github.com/joshuasundance-swca/ai_changelog) |
| aichangelog（Rust，OpenAI 生成） | 1 star，5 次提交，**2025-08-02 被作者归档** | [GitHub](https://github.com/dikkadev/aichangelog) |
| logedit（Python，OpenAI 生成） | **仓库地址返回 404**，已删除或转为私有 | [GitHub](https://github.com/gregpriday/logedit) |
| kacs（Python，LLM 分类生成） | 0 star，0 fork，28 次提交 | [GitHub](https://github.com/atasoglu/kacs) |
| clgen（LLM 生成，多受众） | 0 star | [GitHub](https://github.com/CMXX648/clgen) |
| command-changelog（Claude Code 命令） | 1 star，0 fork | [GitHub](https://github.com/claude-commands/command-changelog) |
| changeish 发布帖（2025-06-17） | 1 条评论，内容是竞品 Shiplog 的广告 | [dev.to](https://dev.to/itlackey/changeish-automate-your-changelog-with-ai-45kj) |
| changelogai 发布帖（3 月 28 日） | 1 条评论，同样是 Shiplog 的广告；作者未给任何下载或 star 数 | [dev.to](https://dev.to/uratools/i-built-a-changelog-generator-that-works-on-any-repo-no-conventional-commits-needed-1ekc) |
| Proseflow 发布帖（4 月 26 日，免费） | 0 条评论，无使用数据 | [dev.to](https://dev.to/shiplogbot/show-hn-i-built-a-free-ai-tool-that-generates-changelogs-from-github-commits-3-tones-12o6) |

九个里有两个归档、一个消失，其余的 star 不超过 1；三篇发布帖收到的全部回应是同一个竞品的两条广告。唯一有一点关注度的是 llm-git（62 star，[GitHub](https://github.com/can1357/llm-git)），而它的主功能是生成提交信息，变更日志只是附带功能。**这不是"没有数据"，而是关于需求的发现：这个东西被反复做出来，没有一次引起注意。**

另有一篇 Indie Hackers 上"我在做一个简化变更日志的工具"的帖子，页面返回 403，没能读到内容，不计入上表。

**需求侧信号**：一个 38 分、约 50 条评论的 Ask HN 帖子（约 11 个月前）专门讨论发版说明怎么写（[HN](https://hn.svelte.dev/item/46257486)）：
- 多人直接推荐 git-cliff、release-please、Changesets
- 多人说"没人读"（"No one read them"、"Hardly anyone reads them"）
- 有人认为初稿可以交给 LLM，但都强调需要人工审阅
- **没有一条评论表示愿意为工具付费**

中文社区（V2EX 等）这一轮搜索没有搜到相关抱怨帖，记为"无信号"，不是"没有需求"的证明。

## 评分卡

基准：面向独立开发者的商业产品。5 分为有利一端。

| 维度 | 分数 | 理由 | 依据 |
|---|---|---|---|
| 痛点强度 | 2 | 麻烦是真的，但属于"有了也不错"；讨论帖里多人说没人读，没人说愿意付钱；同类工具发布后无人回应 | 事实：上述 HN 帖、墓地表 |
| 市场规模 | 2 | 独立开发者数量无可靠公开数据；可触达渠道（HN、dev.to、Indie Hackers）已被同类发布帖占满，且这些帖子几乎没有回应 | 事实：发布帖 0 评论；人数为推测 |
| 差异化与壁垒 | 1 | 免费的现成产品已经在做，其中一个来自平台方本身；功能可以被一个 Markdown 提示词文件复制；"凭什么更好"一行填不出来 | 事实：见"已经有什么" |
| 技术可行性 | 5 | 读 git log、调用 LLM、写文件，全是成熟组件 | 推测，但有 8 个现成实现佐证 |
| 成本与工作量 | 5 | 一个人几个周末可完成；运行成本接近零（见下） | 推测 |
| 变现路径 | 1 | 单次成本不到 1 美分，没有加价空间；用户自带 API key 时你没有任何可收费的环节；同类 CLI 全部免费 | 事实：定价；推测：token 用量 |
| 风险 | 3 | 把私有仓库的 diff 发给第三方模型会让一部分用户拒绝；平台方继续内置该功能的方向明确 | 事实：VS Code 的发版说明已由 Copilot 生成（[报道](https://visualstudiomagazine.com/articles/2026/09/17/vs-code-release-notes-now-generated-by-copilot-might-contain-inaccuracies.aspx?p=1)） |

**单位成本估算**：Claude Haiku 5.5 的官方价格为输入 $0.10、输出 $0.50 每百万 token（[Anthropic 定价页](https://platform.claude.com/docs/en/about-claude/pricing)）。假设一次发版包含 50 个提交、约 2 万输入 token 和 1 千输出 token（token 数是我的估计），成本约为 $0.002 + $0.0005 = **$0.0025**。这对用户是好事，对你的收费模式是坏事。

**分数如何推出结论**：有适用的维度是 1 分，所以不能给「值得做」。这是商业产品，且 1 分落在「差异化与壁垒」和「变现路径」上，对应「不建议做」或「缩小范围做」；这里没有一个更小的版本能绕开这两个问题（范围再小，仍然是同一件免费的事），所以是「不建议做」。技术可行性和成本的两个 5 分只说明它能做出来。

## 这个想法最可能死在哪里

1. **发布当天就没有回应。** 你在 HN / dev.to 发布，得到的第一条评论是"这和 git-cliff 加一句提示词有什么区别"，或者根本没有评论。早期信号：发布帖一周内的评论和 star 数是个位数——这正是墓地表里九个项目的现状。
2. **用户已经在用的工具把它做成了一句话。** 目标用户（会用 CLI 的独立开发者）大概率已经在用 Claude Code、Cursor 或 Copilot，对它说"根据上次 tag 以来的提交写更新说明"就完成了，不需要安装任何东西。早期信号：试用者反馈"我直接让 AI 写了"。
3. **有人用，但没有人付钱。** 即使有了几百个用户，CLI 加自带 API key 的形态里没有可以收费的地方；想收费就必须做托管页面、小组件、邮件推送——那是另一个产品，而且那个市场里已经有 $19 每月的 Shiplog。早期信号：加上付费墙后转化为零。

## 结论与下一步

**不建议做**成商业产品。原因不是做不出来，而是做出来之后和免费方案没有区别，也没有收费的位置。

**一个相邻的、更值得做的方向**：调研里唯一有人付钱的环节是"把更新送到最终用户面前"（应用内小组件、邮件），而不是"生成文字"。但这个方向同样已有多家产品，最低 $19 每月，所以它只是"比原想法好"，不是"值得做"——要走这条路，需要先重新做一次评估，重点是你能否找到一个现有产品没覆盖的具体人群。这一条是我的推测，没有为它单独做调研。

**这周可以做的事**：
- 如果你只是自己发版时嫌麻烦：安装 git-cliff，或者在 Claude Code 里放一个 `/changelog` 命令文件，十分钟解决，不需要立项。
- 如果你仍想验证商业可能性：先不写代码，找 10 个发过版的独立开发者，问"你上次发版说明是怎么写的、花了多久、有没有为此付过钱"。有 3 人以上说付过钱或愿意付，再回来重新评估。

**如果假设不成立**：若这是一个自用或练手项目，「市场规模」和「变现路径」改标"不适用"，结论变为**值得做**——它小、技术确定、一个周末能完成，是很好的练手题目。

## 来源

访问日期均为 2026-11-16。

- git-cliff 仓库：https://github.com/orhun/git-cliff
- git-cliff / release-please / auto 对比（2026-04-22）：https://www.pistack.xyz/posts/2026-04-22-git-cliff-vs-release-please-vs-auto-self-hosted-changelog-generator-guide-2026/
- GitHub 自动生成 release notes 文档：https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes
- ai_changelog（已归档）：https://github.com/joshuasundance-swca/ai_changelog
- aichangelog（已归档）：https://github.com/dikkadev/aichangelog
- logedit（404）：https://github.com/gregpriday/logedit
- llm-git：https://github.com/can1357/llm-git
- changelogai 发布帖（3 月 28 日，年份页面未显示）：https://dev.to/uratools/i-built-a-changelog-generator-that-works-on-any-repo-no-conventional-commits-needed-1ekc
- GitHub Changelog（2026-06-18）：https://github.blog/changelog/2026-06-18-generated-release-notes-credit-you-for-copilot-pull-requests
- github/copilot-release-notes：https://github.com/github/copilot-release-notes
- VS Code 发版说明由 Copilot 生成（2026-09-17）：https://visualstudiomagazine.com/articles/2026/09/17/vs-code-release-notes-now-generated-by-copilot-might-contain-inaccuracies.aspx?p=1
- command-changelog（Claude Code 命令）：https://github.com/claude-commands/command-changelog
- /draft-release-notes 命令：https://heyclau.de/entry/commands/release-notes-drafting
- kacs：https://github.com/atasoglu/kacs
- clgen：https://github.com/CMXX648/clgen
- changeish：https://dev.to/itlackey/changeish-automate-your-changelog-with-ai-45kj
- 免费 AI 变更日志工具发布帖（4 月 26 日，年份页面未显示）：https://dev.to/shiplogbot/show-hn-i-built-a-free-ai-tool-that-generates-changelogs-from-github-commits-3-tones-12o6
- 独立 SaaS 变更日志工具现状（7 月 5 日，作者为 Shiplog 开发者）：https://dev.to/kavinjeya/the-state-of-changelog-tools-for-indie-saas-in-2026-2f22
- Beamer 定价（第三方）：https://www.productlift.dev/blog/beamer-pricing/
- Ask HN：发版说明怎么写：https://hn.svelte.dev/item/46257486
- Anthropic 官方定价：https://platform.claude.com/docs/en/about-claude/pricing
