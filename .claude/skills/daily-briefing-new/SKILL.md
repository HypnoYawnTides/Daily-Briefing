---
name: daily-briefing-new
description: |
  AI 早报 Daily-Briefing 站点的"新增一期"工作流。当用户需要发布新一期日报、更新早报、写下一刊 DAILY、出新一期简报时必须使用此 skill。触发词包括但不限于：更新早报、写新一期、发 DAILY、出新一刊、briefing 更新、新一期早报、/daily-briefing-new。适用场景：已有 Daily-Briefing 静态站（包含 index.html / feed.html / archive.html / builders.html / 两篇 insight 页模板），需要把"当前最新期"从 DAILY NNN 升级到 DAILY NNN+1，涉及重命名旧 feed、新建新 feed、两篇新 insight、三处导航文件同步、旧 insight 回链修正、git 提交 + Vercel 自动部署。
---

# Daily-Briefing · 新增一期

你正在执行"AI 早报"（Daily-Briefing）这个静态站点的**新增一期**工作流。

## 角色定位

这个站点是面向产品经理的 AI Builder 动态速览——每期精选战略层（CEO / 创始人）+ 产品层（PM / 设计师 / 技术创始人）的**真实公开发言**，配 Lenny Daily 风格的编辑解读与主题聚合洞察。你作为编辑的任务：

1. 用 WebFetch 从预设的 builder 博客池抓取新发文
2. 校验源头真实可达、去重已发过的内容
3. 跟用户确认选哪 3 条战略 + 3 条产品 + 2 个洞察主题
4. 写 HTML（feed.html + 两篇 insight-*.html）
5. 同步更新 index / archive / builders 的导航指向
6. git commit + push（用户拍板后）触发 Vercel 自动部署

## 🚨 三条硬约束（绝不可违反）

### 1. 源头必须真实

- **所有外链只能来自 WebFetch 当场抓取到的 URL**，禁止凭记忆或训练语料构造 URL
- 如果 WebFetch 失败、页面 404、日期缺失，直接弃用该候选，不要"近似猜一个"
- 日期只能来自抓取结果里的 `published_date` 字段。如果爬不到确切日期，HTML 里写"日期未标注"（参考现有 Altman 条目的处理）
- **写完 HTML 后必做一次 grep 自检**：从新写的 feed.html 和 insight-*.html 里抽出所有 `href="https://..."`，逐一确认存在于 WebFetch 候选池；不在池内的一律阻断 commit

### 2. 不要重复已发过的内容

- 新一期的外链必须和 `feed-*.html` 里已经用过的**完全去重**
- 检查方式：`grep -rh 'href="https://' feed*.html | sort -u` 出历史已用链接；新候选逐一比对
- 如果某个 builder 没有新内容，就这一期不收录该 builder，不要把旧内容"改个措辞重发"

### 3. 文件架构保持不变

**命名约定**：
- `feed.html` 永远是"当前最新期"
- 历史期归档为 `feed-NNN.html`（NNN = 期号，三位数字）
- 新增一期流程 = `feed.html` → `feed-旧期号.html` + 重写 `feed.html` 为新期
- 这个约定让用户分享 URL 永远是稳定的 `feed.html`，所有上一期 bookmark 也不会断

---

## 候选源池（16 家，可在本 SKILL.md 内编辑扩展）

**战略层 / 基座模型公司**：
1. Anthropic News · https://www.anthropic.com/news
2. OpenAI Blog · https://openai.com/blog
3. Sam Altman · https://blog.samaltman.com/
4. Mistral · https://mistral.ai/news/
5. DeepMind · https://deepmind.google/discover/blog/
6. HuggingFace · https://huggingface.co/blog
7. Perplexity · https://www.perplexity.ai/hub

**产品层 / 工具公司**：
8. Cursor · https://cursor.com/blog
9. Linear · https://linear.app/blog
10. Replit · https://blog.replit.com/
11. Vercel · https://vercel.com/blog
12. Figma · https://www.figma.com/blog/
13. Notion · https://www.notion.so/blog
14. GitHub · https://github.blog/

**个人观察者 / 长文作者**：
15. Simon Willison · https://simonwillison.net/
16. Andrej Karpathy · https://karpathy.github.io/ (+ karpathy.ai)

未来想加新 builder 只改本文件的这一节即可。

---

## 执行流程（7 个阶段）

### 阶段 0 · 摸底（全自动）

```
1. cd 到 Daily-Briefing/ 目录
2. 跑 git status 确认工作区干净（有未提交改动要先告知用户）
3. 扫 feed*.html：最大的期号 N → 新期号是 N+1（三位数字，比如 005 → 006）
4. 读 feed.html（当前最新期，即将变成 feed-NNN.html）了解结构和文案调性
5. 读 insight-*.html 任选一篇了解详文结构
6. 算今天的日期（YYYY-MM-DD + 星期，用中文 Mon/Tue/Wed/Thu/Fri/Sat/Sun 的英文缩写）
```

### 阶段 1 · 候选源抓取（全自动，并行）

- **并行** WebFetch 上述 16 个 URL 的首页
- Prompt 统一问："列出这个博客近 5 天发布的文章标题、作者、发布日期和完整链接"
- 合并所有结果到一个候选清单

### 阶段 2 · 校验与去重（全自动）

1. 剔除掉日期超过 7 天的
2. 跟 `grep -rh 'href="https://' feed*.html` 去重（已发过的不进候选）
3. 分组：战略层 / 产品层 / 个人观察（按源池分类对应）
4. 如果某一层候选不足 5 条（为了留给用户选择空间），回报用户"今天 X 层抓到太少，是否要放宽日期到 10 天"

### 阶段 3 · 向用户呈现候选清单（停下来等回应）

**🛑 STOP-AND-ASK ①**

以表格形式呈现（分战略层 / 产品层 / 个人观察）：

```
| 层 | 日期 | 发布方 | 标题 | 链接 |
|---|---|---|---|---|
| 战略 | 2026-XX-XX | Anthropic | ... | ... |
...
```

然后问用户：

- 从战略层挑 3 条（请按序号或名字告诉我）
- 从产品层挑 3 条
- 2 个 insight 主题：可以是"某一层的共同母题"或"跨层的对比观察"，描述一句即可
- 今天的叙事骨架（briefing 标题 + 3 行速览的方向）想自己先定还是让我起草？

### 阶段 4 · 内容草稿（生成后停一次）

**🛑 STOP-AND-ASK ②**

基于用户选定的 3+3+2 素材，先生成**骨架草稿**：

```
期号: DAILY NNN
日期: YYYY-MM-DD Wed
主标题: (一句话点出本期核心叙事)
Today's Briefing 三行速览:
  01. (战略层主线)
  02. (产品层主线)
  03. (洞察导出)

每条战略/产品卡的核心论点（不要完整正文，只给"这张卡想让读者带走什么"）:
  S1: ...
  S2: ...
  ...

两篇 insight 的 lede + synthesis 核心句:
  Insight 1: ...
  Insight 2: ...
```

然后问用户："文案调性 / 角度 / 标题 / 任一句话要调整吗？" 等用户 OK 才继续。

### 阶段 5 · 执行文件改动（全自动，有序 10 步）

严格按以下顺序，每步完成后立即报告：

1. `git mv feed.html feed-NNN.html`（NNN = 上一期号，即现在的旧期）
2. 修两个旧期的 insight 页 Back 链接：`href="feed.html"` → `href="feed-NNN.html"`
   - 用 grep 先定位：`grep -l 'back-link.*feed\.html' insight-*.html`
   - 只改最新一期对应的那两篇（不要改历史期的，那些已经有正确的 `feed-00X.html` 指向）
3. 写新 `feed.html`：
   - topbar `Daily · NNN+1`、page-head 今日日期 / 今日精选、briefing 三行速览
   - 3 条战略卡 + 3 条产品卡 + 2 条 insight 卡（insight 卡链接到两个新 insight 页）
   - 严格复刻 `feed-NNN.html` 的 CSS 和结构，只改 `<main>` 内的内容
4. 写 `insight-<主题1>.html`（命名建议：`insight-<kebab-case-主题>.html`）
   - topbar `Daily · NNN+1`、back-link `href="feed.html"` (指向当前最新期)
   - 结构：kicker / title / sub-en / meta / lede / builder-row / § 01-03 / § 04 Synthesis / references
5. 写 `insight-<主题2>.html`（同上）
6. 改 `index.html`：Latest Issue 块的编号 / 日期 / 标题 / meta 四项
7. 改 `archive.html`：
   - topbar tag → 新期号
   - page-meta: Issues 数 +1、最新日期更新
   - 顶部新增新期 Current 行（href="feed.html"）
   - 原 Current 行：去掉 "Current" tag、href 改为 `feed-NNN.html`
8. 改 `builders.html`：topbar tag → 新期号
9. 自检 grep：验所有新 href 都在候选池；验所有 `feed-NNN.html` 文件真实存在；验所有 insight 页 Back 链接有效
10. `git status` 汇报改动面（预期 rename + 3M + 3U 左右）

### 阶段 6 · 浏览器人工核验（打开后停下）

**🛑 STOP-AND-ASK ③**

```bash
open feed.html
open index.html
```

提供核验清单（5 条视线，和第一次实战一致）：

1. 顶栏标签、页头日期、meta 统计正确
2. Today's Briefing 三行速览
3. 战略 3 + 产品 3 + 洞察 2 的卡片都渲染，外链点击跳转
4. 两篇新 insight 页：详文、Back 链接回到 feed.html
5. archive 里新期在顶 Current、上一期降级 + href 指向 feed-NNN.html

等用户确认"OK"或说出要改的地方。

### 阶段 7 · git commit + push（两道确认）

**🛑 STOP-AND-ASK ④** commit message 模板：

```
Add Daily NNN issue (YYYY-MM-DD): <主标题浓缩>

- 新增 DAILY NNN 主 Feed (feed.html)：战略 3 + 产品 3 + 洞察 2
  · 战略层：<简述 3 条>
  · 产品层：<简述 3 条>
- 新增两篇洞察详文：
  · insight-<主题1>.html —— <主题1 一句话>
  · insight-<主题2>.html —— <主题2 一句话>
- 重命名 feed.html → feed-NNN-1.html 保留历史期刊
- 更新 index.html / archive.html / builders.html 顶栏与 Latest Issue 指向
- 修正两个旧 insight 页 Back 链接到 feed-NNN-1.html
```

把 message 给用户过目，改了再 commit。

**🛑 STOP-AND-ASK ⑤** push 要不要做？

- 告诉用户：push 到 origin/main 会触发 Vercel auto-deploy（30-60 秒内全世界可见）
- 等用户说"push"才执行 `git push origin main`
- 否则停留在本地 commit 状态即可

---

## 常见坑 · 自检清单

1. **rename 被识别为 delete + add**：`git mv` 之后如果又修改了同名文件（比如新写 feed.html），git 的 rename 检测可能会失效，看到 `create mode 100644 feed-XXX.html` 而非 `renamed:`。这不影响内容，`git log --follow` 仍能追溯历史。不要试图"修正"它。

2. **旧 insight 页 Back 链接漏改**：每期新发时，**只**修改紧邻上一期的两篇 insight（即你刚把 feed.html 重命名成 feed-NNN.html 的那期的 insight 页），不要去动再往前的历史期 insight 页——那些的 Back 链接早就是正确的 `feed-00X.html` 指向。错改会把历史期的 Back 变成指向更早期。

3. **builders.html 的顶栏 tag 容易漏改**：它是全局 Builders 索引，顶栏的 `Daily · NNN` 必须跟着升级。

4. **index.html 的顶栏 tag 不用改**：它是 `Est · 2026`，是固定创刊标识，不随期号走。不要把它错改成 `Daily · NNN`。

5. **无新内容的日子**：如果 WebFetch 回来候选池经校验后不足 6 条（战略 + 产品），明确告诉用户"今天 builder 圈比较冷，候选不足，建议今天不发，或者放宽到 10 天、或者手工补充 X 条"。不要凑数、不要重复老内容。

6. **日期写错星期**：写 feed.html 前先用 `date` 命令或心算验证"今天是星期几"，写 `Mon / Tue / Wed / Thu / Fri / Sat / Sun`（三字母），不要写全名，保持和历史期一致。

7. **Vercel 部署状态**：push 后告知用户"Vercel 会在 30-60 秒内完成部署"。不要自己去主动轮询 Vercel 状态，告诉用户去 Vercel Dashboard 自己看 Deployments 列表即可。

---

## 成功标准

执行完这个 skill，应该满足：

- [ ] `feed.html` 是全新一期（DAILY NNN），3+3+2 结构完整
- [ ] 所有外链真实可达，无一凭空编造
- [ ] `feed-NNN-1.html` 保留旧期完整内容
- [ ] `index.html` Latest Issue 区指向新期
- [ ] `archive.html` 新期在顶（Current），上一期降级（href → feed-NNN-1.html）
- [ ] `builders.html` 顶栏更新
- [ ] 两个旧 insight 页 Back 链接指向 `feed-NNN-1.html`
- [ ] 两篇新 insight 页结构完整（lede / sections / synthesis / references）
- [ ] 一条干净的 feature commit（英文 subject + 中文 body，含 Co-Authored-By）
- [ ] 用户明确同意后 push，Vercel 线上一分钟内切到新期

---

## Session footer reminder

这个 skill 每阶段都有显式 STOP-AND-ASK。**不要跳过任何一个 stop**——用户明确表达过"每一步都要过问、不要自行推进"。哪怕看起来都该过关的地方，也要问。
