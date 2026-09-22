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

- **Git 仓库**：`Desktop\intro`（分支 `main`），最近一次提交 `8cf072f "Add M8Rage personal intro page"`，**当前 `index.html` 有未提交的修改**。
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

## 4. 当前最新状态（最近几次改动后的结果）

1. **三个「关于/技能/联系」胶囊按钮已彻底删除**（HTML 和 CSS 都删干净了）。**不要再重建它们。**
2. **Hero 的三个 CTA 按钮**（第 405–409 行）现在是：
   ```html
   <div class="cta">
     <a class="btn btn-primary" href="#about">关于我</a>
     <a class="btn btn-ghost" href="#skills">我的特长</a>
     <a class="btn btn-ghost" href="#contact">联系我</a>
   </div>
   ```
   点击是**纯锚点平滑滚动**（`href="#about"` 等），无 JS 干预。
3. **导航标签文字**已从 `TEST1`/`TEST2` 改为 `首页`/`其他`（内部 `data-page` 仍是 `test1`/`test2`，不要改）。
4. **JS 里有一段死代码**（第 562–572 行）：
   ```js
   document.querySelectorAll('.scroll-tab').forEach(...)  // 已无匹配元素
   ```
   无害，可顺手清理，但不是必须。

## 5. 历史沿革 & 踩过的坑（务必读，避免重复返工）

- 用户曾**多次反复**调整「三个胶囊按钮（关于/技能/联系）」的位置和样式：居中右侧 → 右上 → 竖排 → 无背景 → 重新设计成胶囊 → **最终删除**。结论就是：**这三个胶囊不再需要**，别再加回来。
- `TEST1/TEST2`（现「首页/其他」）是**页面切换**；「关于/技能/联系」原本是**滚动跳转**，但已删除，其滚动跳转功能现在由 hero 的三个 CTA 按钮承担。
- `test.png`（红圈/青圈标注图）是用户早期用来指定按钮位置的，**该需求已废弃**，不要再纠结这张图。
- 用户以中文交流，偏好简洁直接的改动；不确定时先做最合理的默认实现并清楚说明，让用户指正，而不是反复追问。

## 6. 可能的下一步（未确认，供参考）

- `TEST2`（「其他」页）内容目前是**占位**，可能需要填充真实内容。
- **Git**：`index.html` 有未提交修改，用户之前表示想保存到 git；可能需要 `commit`。
- 之前提过想把 `Desktop\intro` 仓库关联到 **GitHub remote**（尚未做）。
- 可顺手清理第 562 行起的 `.scroll-tab` 死代码。

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
