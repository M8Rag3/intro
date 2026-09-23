# 项目交接文档 — M8Rage 个人介绍页

> 本文件写给下一位接手这个项目的 agent 阅读。目标是让你在**零上下文**的情况下快速了解项目现状、架构、以及已经踩过的坑，避免重复返工。

---

## 1. 项目概述

- **是什么**：一个「M8Rage」的个人介绍静态网页，主题是 Emilia / Re:Zero 的紫色系（zh-CN）。
- **技术栈**：纯单文件 `HTML + CSS + JS`，**无框架、无构建、无依赖**。直接双击 `index.html` 用浏览器打开即可预览。
- **当前状态**：可用，首页各区块（关于/技能/联系）和页面切换都正常工作。

## 2. 文件位置

| 文件 | 路径 | 说明 |
|---|---|---|
| **主文件** | `C:\Users\M8Rage\Desktop\intro\index.html` | 唯一需要改的文件 |
| 弹窗图片 | `C:\Users\M8Rage\Desktop\intro\isacc.png` | 开场弹窗里的图 |
| 备份 | `C:\Users\M8Rage\Desktop\intro\index-backup.html` | 早期备份，可忽略 |
| 标注参考图 | `C:\Users\M8Rage\Desktop\test.png` | 用户早期用来圈位置的红/青圈截图，**需求已废弃，不再使用** |

- **Git 仓库**：`Desktop\intro`（分支 `main`），已关联远程 `origin` → `https://github.com/M8Rag3/intro`（公开）。
- **GitHub Pages**：已开启，公网链接 `https://m8rag3.github.io/intro/`（main 分支根目录，push 后约 1–2 分钟自动更新）。
- **gh CLI**：已安装并以 `M8Rag3` 登录（`gh auth status` 可查）。注意：交接文档里的行号是旧版速查，改过几轮后可能有几十行偏移，以内容 grep 为准。
- **无关项目（不要碰）**：`C:\Users\M8Rage\Desktop\cursor\test\emilia-pomodoro.html`，一个番茄钟，在另一个仓库（分支 `master`），与本项目无关。

## 3. 页面结构与架构

### 3.1 页面切换（不是滚动）
顶部导航 `<nav id="nav">`（约第 382 行）有两个标签，是 **SPA 式页面切换**：

```html
<li><a href="#test1" class="page-tab active" data-page="test1">首页</a></li>
<li><a href="#test2" class="page-tab" data-page="test2">其他</a></li>
```

- `首页`（`data-page="test1"`）→ 显示 `#site`（主要内容）。
- `其他`（`data-page="test2"`）→ 显示 `#test2`。
- 切换靠 JS 给 `#site` 加 `.hidden`、给 `#test2` 加 `.active` 实现。

### 3.2 DOM 结构（关键 id）
```
<div id="site">                         ← 首页主要内容容器（约第 390 行）
  <header id="home" class="hero">       ← 首屏 hero（第 391 行）
  <section id="about">                  ← 关于（第 413 行）
  <section id="skills">                 ← 技能（第 454 行）
  <section id="contact">                ← 联系（第 492 行）
</div>
<section id="test2" class="page">       ← 「其他」页（第 521 行，占位内容）
<footer>                                ← 始终可见
```

### 3.3 JS 核心逻辑（约第 542–582 行）
- `showPage(id, updateHash)`（第 546 行）：切换页面 + 滚动回顶部 + `history.replaceState` 同步 hash（`#test1` / `#test2`）。
- `.page-tab` 点击 → `showPage(data-page)`。
- `#homeLink`（logo「M8Rage」）点击 → 回首页顶部。
- 初始页根据 `location.hash` 决定。

### 3.4 样式要点
- CSS 变量：`--purple-900 … --purple-50`、`--ice`、`--grad-hero`、`--grad-accent`、`--radius: 18px`。
- 平滑滚动：`scroll-behavior: smooth` + `scrollIntoView` + 区块 `scroll-margin-top: 70px`。
- 响应式断点：`@media (max-width: 768px)` 和 `(max-width: 420px)`。
- 主按钮 `.btn-primary`（白底），描边按钮 `.btn-ghost`（透明 + 白描边）。

## 4. 当前最新状态

0. **（2026-09-23 新增）「其他」页番剧卡下方已有三张竖排卡片**：Re:0（爱蜜莉雅）/ 无职转生（洛琪希）/ 春物（由比滨结衣）。
   - 样式类：`.anime-row` / `.anime-card` / `.poster`，左图（108px 宽，整图不裁切、全脸可见）右文。
   - **宽度对齐**：`.anime-row` 用 `max-width: calc((100% - 44px) / 3)`，正好与上方 contact-grid 单列（番剧卡）齐平、左对齐在其正下方；≤768px 时 `max-width: 100%`。
   - 图片：`img/emilia.jpg`、`img/roxy.png`、`img/yui.png`（230×345，来自 AniList 角色图 CDN s4.anilist.co，可直连）。
   - 文案是我写的默认稿，用户未逐字确认。
1. **三个「关于/技能/联系」胶囊按钮已彻底删除**（HTML 和 CSS 都删干净了）。**不要再重建它们。**
2. **Hero 三个 CTA**：「关于我 / 我的特长 / 联系我」，纯锚点平滑滚动，无 JS 干预。
3. **导航标签**：`首页`/`其他`（内部 `data-page` 仍是 `test1`/`test2`，不要改）；另有 🌙 主题切换按钮。
4. **已上线功能**（后续提交加入）：暗色模式（`data-theme="dark"` + localStorage）、开场弹窗（`#introModal`）、加载动画（`#loader`）、头像 `img/emt.jpg`、技能卡片配图 `img/*.png`。
5. **「其他」页（`#test2`）已填充**：番剧/游戏/剪辑三张卡（复用 `contact-grid`/`contact-card`）+ `.other-block` 经历列表（复用 `highlight-list`）。内容是 agent 按已知信息写的默认稿，**用户未逐字确认，可能要改**。
6. **「关于我」的 highlight-list** 模板占位文案已替换成实际内容。
7. `.scroll-tab` 死代码已清理。

## 5. 历史沿革 & 踩过的坑（务必读，避免重复返工）

- 用户曾**多次反复**调整「三个胶囊按钮（关于/技能/联系）」的位置和样式：居中右侧 → 右上 → 竖排 → 无背景 → 重新设计成胶囊 → **最终删除**。结论就是：**这三个胶囊不再需要**，别再加回来。
- `TEST1/TEST2`（现「首页/其他」）是**页面切换**；「关于/技能/联系」原本是**滚动跳转**，但已删除，其滚动跳转功能现在由 hero 的三个 CTA 按钮承担。
- `test.png`（红圈/青圈标注图）是用户早期用来指定按钮位置的，**该需求已废弃**，不要再纠结这张图。
- 用户以中文交流，偏好简洁直接的改动；不确定时先做最合理的默认实现并清楚说明，让用户指正，而不是反复追问。

## 6. 进行中的任务（2026-09-23 中断，恢复时从这里继续）

### 6.1 「游戏」三张卡（用户已点名：逃离塔科夫 / 以撒的结合 / 无畏契约）
- **目标**：在「其他」页游戏卡下方加三张同款式卡（结构同番剧那三张，各自对齐到游戏卡正下方）。
- **计划的布局改法**：把现在的 `.anime-row`（只有番剧一列）重构为三列的外层网格（如 `.under-grid`，`repeat(3,1fr)` gap 22px，与 contact-grid 一致），每列内部竖排卡；番剧列放现有三张，游戏列放新三张，之后剪辑列同理。**注意：用户明确要「竖向排列、宽度与上方单卡齐平」，别改回横排。**
- **图片进度**：
  - ✅ `img/isaac.jpg`（以撒，300×450 竖版，Steam CDN `cdn.cloudflare.steamstatic.com/steam/apps/250900/library_600x900.jpg`，可直连）
  - ✅ `img/valorant.png`（无畏契约，600×900，官方 playvalorant CMS `cmsassets.rgpub.io`，蕾娜+雷兹双人图，已验证内容）
  - ❌ **逃离塔科夫的图还没搞定**。已排除/失败的路：escapefromtarkov.com 403（Cloudflare）、battlestategames.com HEAD 通但 GET 000、fandom/baike/taptap 图床不通或反爬、百度图片反爬（Forbid spider）、Bing 图片搜返回的前几个结果是垃圾（BMI 图等，需换关键词/取更多结果验证内容再下载，下载后必须用 Read 看图确认是塔科夫）。
  - 可用图床白名单：`s4.anilist.co`、`cdn.cloudflare.steamstatic.com`、`cmsassets.rgpub.io`、`i0.hdslb.com`、`static.wikia.nocookie.net`（CDN 通但拿不到页面）、`tse*.mm.bing.net`、raw.githubusercontent.com、SteamGridDB 的 `cdn.steamgriddb.com`（通，但没翻到具体资源 id）。
  - Steam 全站（community/store/api）校园网全墙，**拿不到用户 Steam 库**；但 Steam 图床通。
- **卡片文案**也还是默认稿待写。

### 6.2 「剪辑」三张卡（等用户给素材）
- 目标：剪辑卡下方同样三张。
- 需要用户给 3 个他的 B 站投稿链接（形如 `https://www.bilibili.com/video/BVxxxx`）——单视频接口 `x/web-interface/view?bvid=` 实测可用（可拿标题+封面）；**投稿列表接口（space/wbi/arc/search）被风控 412**，别再试列表接口。
- 用户 B 站 mid：30166441；Steam/B站 vanity 名都是 M8Rage。

### 6.3 其它待办
- 「其他」页所有文案均为默认稿，待用户过目。
- 用户已明确：**改完不要每次自动 push**，本地 commit 即可，用户说推送再推。

## 6.5 环境与网络踩坑（校园网，2026-09-23 实测）

- **不要配代理**（用户原话：校园网会被抓）。
- 通：github.com、api.github.com、anilist、steam 图床、playvalorant/cmsassets、bing、bilibili 图床和单视频接口、cdn.steamgriddb.com、img.tapimg.com、tarkov.dev、taptap.cn 首页。
- 不通/反爬：Steam 全站、fandom 主站、upload.wikimedia.org、百度百科/百度图片（403/反爬）、B 站投稿列表接口（412）、battlestategames GET、lp.wegame.com.cn。
- gh CLI 已装并以 M8Rag3 登录；远程 `origin` = `https://github.com/M8Rag3/intro`（公开）；GitHub Pages `https://m8rag3.github.io/intro/` 已开。

## 7. 快速定位速查

| 内容 | 行号 |
|---|---|
| 顶部导航 | ~382 |
| `#site` 容器 | ~390 |
| hero（含 CTA 按钮） | ~391 / CTA 405–409 |
| `#about` / `#skills` / `#contact` | 413 / 454 / 492 |
| `#test2`（其他页） | 521 |
| `showPage` 函数 | 546 |
| `.scroll-tab` 死代码 | 562–572 |

---

**给下一位 agent 的一句话总结**：这是一个单文件紫色主题个人介绍页，页面切换和滚动跳转都已就绪；最近刚删掉了三个胶囊按钮、把 hero CTA 改成「关于我/我的特长/联系我」三按钮、把导航标签改成「首页/其他」；这些改动**尚未 commit**。
