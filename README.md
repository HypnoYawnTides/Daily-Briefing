# AI 早报 · Daily Briefing

> *Reading the minds behind AI.*
> 给 PM 的全球 AI 圈思考速览

一个面向产品经理的 AI Builder 动态速览站 mockup——每期精选战略层（CEO / 创始人）与产品层（PM / 设计师 / 技术创始人）的公开发言，配 Lenny Daily 风格的编辑解读与主题聚合洞察。

本 repo 是**静态 HTML 设计原型**（mockup），不是运行中的产品。

---

## 在线预览

待 GitHub Pages 启用后：

**https://hypnoyawntides.github.io/Daily-Briefing/**

启用方法：GitHub repo → Settings → Pages → Source 选 `Deploy from a branch`，Branch 选 `main` / `(root)`，保存。等 1-2 分钟后访问上方 URL。

本地预览：clone 后双击打开 `index.html`，或者用任意静态服务器（如 `python3 -m http.server`）。

---

## 页面地图

| 路径 | 页面 | 说明 |
|---|---|---|
| `index.html` | 封面落地页 | 品牌 hero + 本期 Latest Issue 大块 + 黑底 ENTER 入口 |
| `feed.html` | 每日主 Feed | 三分区（战略层 / 产品层 / 洞察）+ Today's Briefing 速览 |
| `builders.html` | Builders 追踪对象 | 8 位 AI builder 索引（战略层 5 + 产品层 3） |
| `archive.html` | 历史期刊 | 4 期列表（DAILY 004 真实内容 + 001-003 结构占位） |
| `insight-ai-human-boundary.html` | 洞察 1 · 主题聚合 | AI 与人的边界：Altman / Amjad / Dario 共同母题 |
| `insight-tool-paradigm-shift.html` | 洞察 2 · 行业观察 | PM 工具与开发工具的范式转向：Linear + Cursor |

---

## 内容与数据说明

**真实性约束**：mockup 里的每一条 builder 动态都指向**真实公开发言**，外链直接跳到对应博客 / 官方 News / 帖子源文。包括：

- Sam Altman · *Abundant Intelligence* · [blog.samaltman.com](https://blog.samaltman.com/abundant-intelligence)
- Dario Amodei · *Where things stand with the Department of War* · [anthropic.com/news](https://www.anthropic.com/news)
- Amjad Masad · *The Future Is Actually Very Human* · [blog.replit.com](https://blog.replit.com/replit-raises-400-million-dollars)
- Karri Saarinen · *Issue tracking is dead* · [linear.app/next](https://linear.app/next)
- Cursor 团队 · *Meet the new Cursor* · [cursor.com/blog/cursor-3](https://cursor.com/blog/cursor-3)
- Cursor 团队 · *Interact with agent-created visualizations in canvases* · [cursor.com/blog/canvas](https://cursor.com/blog/canvas)

**编辑评注**（Feed 卡片正文 + Today's Briefing 三行速览 + 两篇 Insight 聚合稿）是示例口吻的原创文字，用来展示"真实运营时编辑如何加工原文"的方向。

---

## 设计系统

### 色板

- 主背景：`#F1ECDF`（暖米色，底部叠加 radial-gradient noise 营造纸纹）
- 主文字：`#1a1a1a`（近黑）
- 次级强调：`#8a5a2b`（棕色，用于 Insight 的 ◆ 标记与 kicker）

### 字体

- 英文衬线 · 斜体 Hero：**Playfair Display** Italic（大字斜体）
- 英文衬线 · 正文/斜体 accent：Playfair Display Regular
- 中文衬线 · 大标题：**Noto Serif SC**（思源宋体）
- 中文无衬线 · 正文：**Noto Sans SC**（思源黑体）+ PingFang SC / Microsoft YaHei fallback

### 视觉语法参考

- Taste Engine（Signal-Driven Equity Research） —— 米色内参 + 双语 serif + 编号小标
- Financial Times Weekend —— 大字斜体 hero + 克制 masthead
- Economist Espresso —— 每日简报的条目组织方式

---

## 技术

纯静态 HTML 文件。无构建系统、无外部依赖、无 JavaScript（除 Google Fonts CSS 引用外）。直接用浏览器打开即可。

---

## License

本 mockup 内 **代码部分**（HTML / CSS）MIT License 使用。

**引用的 builder 原文** 版权归原作者所有，本 repo 内容仅作编辑示例与聚合指引，外链指向真实源文，不重复或转载原文正文。
