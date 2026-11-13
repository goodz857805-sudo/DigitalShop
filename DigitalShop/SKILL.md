---
name: digital-store
description: 数字化门店 / 服务指南针的 BI 后台页面生成与视觉优化（UEDMaster）。命中「数字化门店」「服务指南针」，或需求属于 BI 后台、报表、看板、指标卡、数据可视化时，以 references/digital-store-spec.md 为执行依据，取值来自 digital-store-tokens.css，技术栈为 Arco 组件 + 覆盖 token。生成强制五步流程（业务场景分析→组件库匹配→组合方案→生成Demo→合规校验报告），顺序不可调换。组件强制：骨架只从 app-shell.html 复制、按钮走 .btn 模板、控件只用组件库、禁止自造组件与页面重定义组件类。生成的 demo 一律窗口自适应：流式布局 + 响应式断点，内容区高度溢出时自动出现滚动条，不按设计稿固定尺寸缩放。同时保留通用能力：对没有专门设计系统的产品直接生成可交互 demo 原型，或对已有 demo（HTML / 前端页面）做排版、留白、层级、色彩、动效、微交互和响应式的升级，并截图自检、持续迭代。当用户要「做个 demo / 原型 / 高保真页面」「把这个原型优化得更好看 / 更专业」「UI 效果差，帮我改」时使用。
metadata:
  version: 0.8.0
  upstream: Leonxlnx/taste-skill · skills/redesign-skill（MIT）
---

# UEDMaster

先判断走哪条依据，再动手。

| 需求类型 | 执行依据 | 栅格节奏 |
|---|---|---|
| 关键词含「数字化门店」 | `references/digital-store-spec.md` | 边距 / 栅格槽 / 卡片内距 **8px** |
| 关键词含「服务指南针」，或两者都无但确属 BI 后台、报表、看板 | 同上 | 边距 / 栅格槽 / 卡片内距 **12px** |
| 通用 demo、落地页、无设计系统的产品 | 本文件下方通用底线 | — |

卡片内距固定**上下左右四边同值**：数字化门店（核心指标达成 / 财务开单 / 全渠道销售 / 全渠道库存等）白卡 `8px`；服务指南针及其他报表类页面白卡 `12px`。两种场景不要混用。

命中前两行时，**先读 `digital-store-spec.md`，再读 `digital-store-tokens.css`**，然后才开始写页面。颜色、字号、行高、间距、圆角、阴影、层级一律从 tokens 取值，不另估。技术栈是 **Arco 组件 + 覆盖 token**，不换组件库；原型与单文件 demo 直接复制模板包 `app-shell.html`（组件样式已内联、只依赖 `tokens.css`，与 Arco 覆盖后的外观同源），增量区块用 `components.css` + `blocks.md`；接入 Arco 项目时按第 9.8 节映射到对应组件，不另写一套外观。

**页面骨架只从模板包取，不照 md 重写组件。** `references/template/` 是组件的唯一实现：

| 文件 | 作用 | 怎么用 |
|---|---|---|
| `app-shell.html` | **可运行的多页模板**：首页入口 + 核心指标达成 + 财务开单，侧导航 / 页签 / 筛选器 / 日期面板 / 图表 / 表格 / AI 全带；组件样式内联，只依赖 `../digital-store-tokens.css` | 复制整页后改 `MENU`、`state`、`homeHtml()` / `pageHtml()` / `fbHtml()` 与 `TAB_META`；数据在脚本顶部 `METRICS` / `AI_ROWS` / `FB_T_*` / `FB_PRICE` 等常量 |
| `components.css` | `.ind` / `.btn` / `.data-table` / `.u-ai` / `.chart` 等组件外观（从模板页抽出） | 已有工程增量接入时 `<link>`，单文件交付时内联；页面里不覆盖 |
| `blocks.md` | 指标卡 / 图表 / 表格 / AI 的可复制片段（自开发与 QBI 两套） | 往已有页面加区块时直接粘 |

生成新页面 = **复制 `app-shell.html` → 改 `MENU` / 页面数据与渲染函数 → 缺区块时从 `blocks.md` 粘**。不要照着 md 里的尺寸表重新拼一套组件 DOM/CSS，那会立刻产生第二套外观。spec md 只用来核对取值与规则。模板缺能力时，先改 `app-shell.html` / `components.css`，再同步规范页与文档，最后回到页面。

**生成强制流程（五步，Skill 内置，顺序固定不可调换、不可跳过）：** 用 PRD 或提示词生成报表页时，一律按以下五步执行；**Step 5 校验报告必须随交付输出，禁止跳过**。

**Step 1 业务场景分析** — 先写清三件事：① 业务目标 / 页面用途 / 用户角色；② 页面模块拆解（头部、筛选、指标卡、图表、表格、弹窗等，逐块列出）；③ 交互类型（静态展示 / 查询筛选 / 表单提交 / 详情弹窗）。产出**模块清单**，供 Step 2 逐项匹配。

**Step 2 检索内部组件库清单** — 遍历 `references/template/`（`app-shell.html` + `components.css` + `blocks.md`，这是组件库唯一实现），把 Step 1 的每个模块映射为一行「**模块名称 → 组件库组件名 + 组件版本**」（组件版本 = `SKILL.md` metadata.version）。⚠️ **禁止新建自定义样式、禁止手写原生 div 样式替代已有组件库组件；只有组件库确实不存在该组件时，才允许新增自定义组件，并在交付说明中标记【新增组件，待入库】。**

**Step 3 组件组合方案** — 说明组件如何嵌套、传参与属性配置：尺寸（32 / 28 等档位）、主题（自开发 / QBI）、状态（默认 / hover / 禁用 / 加载 / 错误）逐项落在组件库已定义的形态上，不另造状态。

**Step 4 生成页面 Demo** — 按 Step 3 方案复制模板骨架，渲染出代码 / UI。

**Step 5 合规校验输出（必须输出校验报告）** — 按三项逐条核对并给出结论：
① 是否全部使用组件库内已有组件，有没有私自写独立样式；
② 组件属性、尺寸、色值、圆角、间距是否和组件库规范完全一致；
③ 交互状态是否匹配组件库定义。
任一项不合格 = 回到 Step 2–4 返工重做，不允许「先交付后补报告」。

**组件强制约束（生成页面前通读一遍，违反即返工）：** 页面只允许「模板骨架 + 组件库 + 页面布局 CSS」三层，不允许出现第四层自造组件。

1. **骨架只从 `app-shell.html` 复制**：只改 `MENU` / `state` / 页面函数 / 数据常量，不重写侧导航、页签、筛选栏的 DOM 与 CSS。
2. **按钮强制走 `.btn` 模板**：只用 `.btn-primary` / `.btn-secondary` / `.btn-outline` / `.btn-text` 四款 + `disabled`，尺寸随控件高度规范（32 / 28）。禁止自写 button 外观，禁止用 div / span / a 伪装按钮。
3. **控件强制用组件库**：下拉 `.select` + `.dropdown`、日期 `.u-dsel-tr` + `.dp-pop`、粒度 `.u-tabs-round`、页签 `.ptab`、指标卡 `.ind`、图表 `.chart`、AI `.u-ai`、表格 `.data-table`、标签 `.tag`。禁止原生 `<select>`、`<input type="date|checkbox|radio">`，禁止自造简化版（灰块顶指标卡、两条线顶图表、纯文字顶 AI）。
4. **页面 `<style>` 白名单**：只允许布局 / 栅格 / 响应式断点 / 图表尺寸 / 页面私有数值（如表格 `min-width`），取值必须引用 tokens 变量；品牌色、字号、行高、圆角、阴影、控件外观一律不许在页面里写死。
5. **禁止重定义组件类**：页面 `<style>` 里不得出现 `.btn` / `.select` / `.dropdown` / `.ind` / `.filterbar` / `.u-ai` / `.data-table` / `.u-tabs-round` / `.page-tabs` / `.sn` / `.dp-pop` 等组件类选择器。组件库缺变体时，先回写 `components.css` / `blocks.md` 并同步规范页与文档，再在页面使用，不许先斩后奏。
6. 图标只用 `references/icons/` 的 492 枚（inline SVG + `currentColor`）；emoji / iconfont / 凭记忆手绘路径一律视为自造组件。
7. 交付前按 `digital-store-spec.md` 第 13 节第 20 条核对「组件是否全部来自组件库」。

**导航、页签、系统名称按需求生成，不沿用模板示例。**
- **导航 = 当前系统的实际菜单树**（`MENU`，最多两级），**页签 = 当前打开着的页面**（`state.tabs`，首页固定最左，其余按打开顺序）。菜单项用 `page` 指向入口页、用 `tab` 指向报表页，跳转只走 `PAGE_OF_TAB` / `TAB_OF_PAGE` 两张映射表，不要在 DOM 里另写一套。模板自带的「首页 / 核心指标达成 / 财务开单」只是结构示例，换系统时整段替换 `MENU` 与页面函数；不要把所有菜单项都塞成页签。
- **系统名称由场景决定**：`data-scene="store"` → 数字化门店，`"compass"` → 服务指南针。其他系统**先向用户确认系统名称**，写入 `<html data-brand="系统名称">`，同时确认 8 / 12 节奏（数字化门店 8，其余默认 12）。未填时模板显示「未命名系统」并在控制台提示，**不要拿「数字化门店」当占位**。
- 菜单图标从页面 sprite 或 `references/icons/` 取，按菜单含义选，不重复用同一个图标凑数。
- 页头能力暴露在 `window.DSShell = { scene, mode, brand, toast }`；页面内提示统一走 `DSShell.toast()`，不要另起一套通知。

第二根轴是**开发模式**，与场景正交：需求含「QBI」→ 用 QBI 规格（指标卡统一 252 宽、按钮品牌实心）；含「自开发」→ 用自开发规格（指标卡 232/237 宽、按钮描边款）；都没说 → 默认自开发，并在交付说明里提一句。尺寸见 `digital-store-spec.md` 第 9 节，取值见 tokens 第 16、17、18 段。

筛选器栏（FilterBar）是**容器不是组件**，放在 container 内容区置顶，由 tabs-capsule + select + datepicker 按业务组合、右侧挂按钮组。两套外观不要混：自开发 `0 8px 0 0` 圆角 + `8px 16px` 内距 + `8px` 间距、字段无标签；QBI 整块 `8px` 圆角 + `8px 12px` 内距 + `12px` 间距、字段带标签。同一行控件高度必须统一（select / 自开发胶囊 32，datepicker / QBI 胶囊 34），下拉面板统一走 dropdown-menu 规格。详见第 9.5–9.7 节。

图标走**尺寸包 + 图标库**两层：尺寸 10/12/14/16/18/20/22/24/32/48 十档，全部 1:1；图标库 9 分类 **492 枚**，文件随 skill 一起分发，路径 `references/icons/<分类>/<名称>.svg`（arrow 36、cover 27、edit 43、general 90、interactive 39、media 16、supplement 202、tips 17、trademark 22）。**缺图标先在这 492 枚里按分类找，不要凭记忆重画**；取到的 SVG 直接内联（`fill: currentColor`）或作为 `<img>` 引用，不要转 iconfont。默认填充 `#4E5969`（= `--text-color-text-2`）**不是品牌绿**，需要绿时用 `primary-7`。导出的 SVG 是填充路径不是 stroke，线宽随尺寸等比缩——**10/12 两档会发虚，慎用**。实现统一 inline SVG + `fill: currentColor` + `viewBox="0 0 24 24"`，尺寸只由 CSS 控；不要一份颜色导一个文件，也不要用 iconfont / emoji 顶替。详见第 9.9 节。

**页面模板 / 多页面 demo：缩略图 = 同一份页面 DOM 的快照，打开的页面按视口自适应，且只拿组件库组件拼。** 做「目录 + 点击进详情」的模板区时：

- 页面里的每个区块都取模板包的现有组件——指标卡 `.ind`、SideNav `.sn`、页签 `.page-tabs`、FilterBar `.filterbar` + DateSelected `.u-dsel` + select + 按钮 `.btn`、趋势图 `.chart`、AI 辅助分析 `.u-ai`、表格 `.data-table` + 标签 `.tag`。**不允许**自造简化版（灰块顶指标卡、两条线顶图表、纯文字顶 AI）——那会让 demo 与组件规范裂成两套外观。
- **缩略图 = 同一份页面 DOM 的等比缩放**（目录里的「图」），不是手绘骨架：`.tpl-thumb-win` 内放 1440×900 的 `.tpl-thumb-doc`，`transform: scale(容器宽/1440)` + `origin 0 0`，内容由与预览页**完全相同的拼装函数**生成，仅把图表从 ECharts 换成 SVG 以免 canvas 缩小发虚。**固定 1440×900 只属于缩略图**，不代表打开后的页面也要按设计稿尺寸定格。
- 交互：点缩略图 → 目录被整页替换（页面全宽挂载、**按视口自适应**，不是弹窗 / iframe，也不是 1440 设计稿整体缩放），左下角悬浮「返回」按钮回目录，`Esc` 也可返回。详见第 10.4–10.5 节。

**所有 demo 都必须窗口自适应，这是硬约束。** 浏览器窗口才是画布，1440×900、1222/1238/1254 这类数字只作设计参考基线：不得写成固定宽高，不得用 `transform: scale()` 缩放整页，不得用 `overflow:hidden` 把超出视口的内容裁掉。宽度流式（flex / grid + `min-width:0`，断点 1240 / 1024 / 760 / 560）：指标卡 `repeat(auto-fill,minmax(230px,1fr))`，筛选器可换行、select 可收缩，≤1024 侧栏转抽屉（遮罩 + `Esc` 关闭），≤760 筛选器 2×2、表格横向滚动。高度以 `100dvh` 为顶：桌面 `height:calc(100dvh - 顶栏)` + 内容区 `flex:1;min-height:0;overflow:auto`，**内容超出时自动出现滚动条、保证完整可见**；≤1024 回到文档流自然滚动。图表按容器实测尺寸绘制（`clientWidth/clientHeight`），`resize` 防抖重绘，不用固定 viewBox 拉伸。详见 `digital-store-spec.md` 第 10.5 节。

优先级：**用户明确指令 > `digital-store-spec.md` > `references/web-baseline.md` > 本文件标记区内的通用审计**。上游清单是启发式审美建议，规范是交付硬约束，两者冲突时规范赢（例如上游说「别用左侧边栏导航」「别用三栏等宽卡片」，这在 BI 后台是正确的，按规范做）。

**8px 是例外，12px 是默认。** 拿不准就用 12px，不要把报表类页面套成 8px 节奏。

未命中上述场景时，按下方「两种用法」与品牌强调色处理。

## 说明文档

数字化门店的说明文档分四章，全部在 `references/guide.md`：

| 章节 | 内容 |
|---|---|
| 介绍 | 为什么需要、能做什么、怎么工作、适合谁、页面模板、有何不同、核心机制 |
| 使用指南 | skill 自动处理的范围、需求怎么说、澄清与确认流程、形态匹配模板、给产品经理 |
| 通用版 | 非 Arco 技术栈如何接入 `digital-store-tokens.css`、各技术栈取值映射、交付前自检 |
| 更新日志 | 版本变更与升级后用法变化（Keep a Changelog 格式） |

交付或介绍本规范时先读 `references/guide.md`；需要引用规则细节再回到 `digital-store-spec.md`。

## 两种用法

- **从需求生成 demo**：先想清楚页面要让用户完成什么、最重要的一屏是什么，再动手。生成后把下方「Design Audit」当作自检清单，逐项排查后修正。
- **优化已有 demo**：按下方「How This Works」的 Scan → Diagnose → Fix 进行。内容、数据和已有交互默认保留；结构调整只为服务视觉与可用性。**默认保留原有明暗主题**（浅色仍浅色、深色仍深色），用户明确要求时才切换。

**一版还是多版**：默认只做一版完整方案。用户没给视觉参考或改动幅度时（从需求生成、或「整页优化」），先做 2–3 个**首屏小样**让用户选，选定后再做完整版：
- 小样之间的差别放在改动幅度或视觉气质上（如保守收敛 / 标准优化 / 大胆重排），不是只换颜色；每个都遵守品牌色与明暗主题规则。
- 每个小样附一句取舍说明，并标出你推荐哪一个。
- 范围已明确、用户给了方向、或说「直接做」「你来定」时，不停下来，直接做一版。无法等待用户回复时（如自动运行），按「标准优化」幅度做一版，最后说明还有哪些方向可选。

两种用法都要**截图自检**：在浏览器里打开页面，至少看桌面宽屏、窄屏与移动宽度，以及弹层、抽屉、空状态等主要状态；看图找问题、修正、再截图，直到没有明显可提升之处。不凭代码想象效果，不以「没有报错」代替「看起来对」。自检时同时对照 `references/web-baseline.md` 的交付底线（点击区域、移动端输入、状态不只靠颜色、长内容、深色主题等）。命中数字化门店场景时，另按 `digital-store-spec.md` 第 13 节的 20 条硬规则逐项检查——那 20 条写的是本规范最容易翻车的地方（红好蓝差、品牌色分档、圆角只有 4/8、开发模式、Tab 层级、筛选器栏与控件高度、图标上色方式、窗口自适应与内容区滚动、组件强制走组件库等）。

## 品牌强调色（仅限通用场景）

**命中数字化门店 / 服务指南针时跳过本节。** 那类页面一律用 `digital-store-tokens.css` 里的
`--primary-primary-6` `#00AAA6`（大色块）、`--primary-primary-7` `#009093`（图标、边框）、
`--primary-primary-8` `#00757B`（链接文字、白字主按钮）和 `--primary-brand-light` `#F2FBFB`（轻量强调背景）——
**不要用下面这组 Tailwind teal 值**，它跟规范无关。分档依据见 `digital-store-tokens.css` 的 02 段注释，
详细推导仍保留在 `references/brand-color.md`（那份文档只用来说明「品牌青绿白底对比度不足」这个判断，
具体色值以 tokens 为准）。

以下仅用于**没有专门规范的通用 demo**。只定义一组强调色，其余颜色由你按页面需要判断。

| 角色 | 色值 | 用在哪里 |
|---|---|---|
| 品牌强调色 | `#009689`（Tailwind teal-600） | 选中指示、图标、进度条、大块色面 |
| 文字 / 按钮强调色 | `#00786F`（Tailwind teal-700） | 链接文字、白字主按钮 |

- 克制使用：青绿只标这一屏最该被注意或点击的东西（主操作、当前选中、关键进度）。一屏只有一个主操作使用强调色块；标题、标签、普通按钮、数字默认用中性色。截图自检时数一下首屏出现青绿的位置，通常不超过 5–8 处，多了就把次要的改回中性色。
- 不要用青绿替换页面上所有的颜色：多个类别并列（图表系列、行业、分类标签）时用不同色相区分，青绿只给重点那一项；红、橙、绿等状态色保留各自色相，只用于表达状态，不当作装饰色。
- 冷暖平衡：青绿偏冷，中性色不要再染青调，用纯灰或略暖的灰来平衡（这条优先于下方「Tint all grays」）。
- 悬停态可以从这两个色派生；图表色阶也可以派生，但只用于同一系列的深浅。
- 深色背景：不要直接套用这两个值（`#00786F` 在深底上对比度不足 3.5:1）。文字、链接、图标改用提亮的派生色（如 teal-400 `#00D5BE`），主按钮用亮青绿底配深色字；派生后核对对比度：正文 ≥4.5:1，图标与边框 ≥3:1。
- teal-600 在白底上对比度约 3.7:1，不能用于正文文字或白字小按钮；需要文字时换 teal-700（约 5.4:1）。
- 这两个值替代了下方通用规则里「挑一个强调色」的选择。
- 通用场景的取值理由与对比度计算见 `references/brand-color.md`；数字化门店场景见 `digital-store-spec.md` 第 3.2 节。

---

<!-- upstream:begin redesign-existing-projects（逐字复用，修改请走 CONTRIBUTING 的上游同步流程） -->
# Redesign Skill

## How This Works

When applied to an existing project, follow this sequence:

1. **Scan** — Read the codebase. Identify the framework, styling method (Tailwind, vanilla CSS, styled-components, etc.), and current design patterns.
2. **Diagnose** — Run through the audit below. List every generic pattern, weak point, and missing state you find.
3. **Fix** — Apply targeted upgrades working with the existing stack. Do not rewrite from scratch. Improve what's there.

## Design Audit

### Typography

Check for these problems and fix them:

- **Browser default fonts or Inter everywhere.** Replace with a font that has character. Good options: `Geist`, `Outfit`, `Cabinet Grotesk`, `Satoshi`. For editorial/creative projects, pair a serif header with a sans-serif body.
- **Headlines lack presence.** Increase size for display text, tighten letter-spacing, reduce line-height. Headlines should feel heavy and intentional.
- **Body text too wide.** Limit paragraph width to roughly 65 characters. Increase line-height for readability.
- **Only Regular (400) and Bold (700) weights used.** Introduce Medium (500) and SemiBold (600) for more subtle hierarchy.
- **Numbers in proportional font.** Use a monospace font or enable tabular figures (`font-variant-numeric: tabular-nums`) for data-heavy interfaces.
- **Missing letter-spacing adjustments.** Use negative tracking for large headers, positive tracking for small caps or labels.
- **All-caps subheaders everywhere.** Try lowercase italics, sentence case, or small-caps instead.
- **Orphaned words.** Single words sitting alone on the last line. Fix with `text-wrap: balance` or `text-wrap: pretty`.

### Color and Surfaces

- **Pure `#000000` background.** Replace with off-black, dark charcoal, or tinted dark (`#0a0a0a`, `#121212`, or a dark navy).
- **Oversaturated accent colors.** Keep saturation below 80%. Desaturate accents so they blend with neutrals instead of screaming.
- **More than one accent color.** Pick one. Remove the rest. Consistency beats variety.
- **Mixing warm and cool grays.** Stick to one gray family. Tint all grays with a consistent hue (warm or cool, not both).
- **Purple/blue "AI gradient" aesthetic.** This is the most common AI design fingerprint. Replace with neutral bases and a single, considered accent.
- **Generic `box-shadow`.** Tint shadows to match the background hue. Use colored shadows (e.g., dark blue shadow on a blue background) instead of pure black at low opacity.
- **Flat design with zero texture.** Add subtle noise, grain, or micro-patterns to backgrounds. Pure flat vectors feel sterile.
- **Perfectly even gradients.** Break the uniformity with radial gradients, noise overlays, or mesh gradients instead of standard linear 45-degree fades.
- **Inconsistent lighting direction.** Audit all shadows to ensure they suggest a single, consistent light source.
- **Random dark sections in a light mode page (or vice versa).** A single dark-background section breaking an otherwise light page looks like a copy-paste accident. Either commit to a full dark mode or keep a consistent background tone throughout. If contrast is needed, use a slightly darker shade of the same palette — not a sudden jump to `#111` in the middle of a cream page.
- **Empty, flat sections with no visual depth.** Sections that are just text on a plain background feel unfinished. Add high-quality background imagery (blurred, overlaid, or masked), subtle patterns, or ambient gradients. Use reliable placeholder sources like `https://picsum.photos/seed/{name}/1920/1080` when real assets are not available. Experiment with background images behind hero sections, feature blocks, or CTAs — even a subtle full-width photo at low opacity adds presence.

### Layout

- **Everything centered and symmetrical.** Break symmetry with offset margins, mixed aspect ratios, or left-aligned headers over centered content.
- **Three equal card columns as feature row.** This is the most generic AI layout. Replace with a 2-column zig-zag, asymmetric grid, horizontal scroll, or masonry layout.
- **Using `height: 100vh` for full-screen sections.** Replace with `min-height: 100dvh` to prevent layout jumping on mobile browsers (iOS Safari bug). BI 框架例外：可以用 `height: calc(100dvh - 顶栏)` 固定框架高度，但内容区必须 `min-height:0; overflow:auto` 接管滚动（见 10.5）。
- **Complex flexbox percentage math.** Replace with CSS Grid for reliable multi-column structures.
- **No max-width container.** Add a container constraint (around 1200-1440px) with auto margins so content doesn't stretch edge-to-edge on wide screens. **例外：命中数字化门店 / 服务指南针的 BI 后台页面**——这类页面铺满视口、宽度流式，不加 `max-width`（见 `digital-store-spec.md` 10.5）。
- **Cards of equal height forced by flexbox.** Allow variable heights or use masonry when content varies in length.
- **Uniform border-radius on everything.** Vary the radius: tighter on inner elements, softer on containers.
- **No overlap or depth.** Elements sit flat next to each other. Use negative margins to create layering and visual depth.
- **Symmetrical vertical padding.** Top and bottom padding are always identical. Adjust optically — bottom padding often needs to be slightly larger.
- **Dashboard always has a left sidebar.** Try top navigation, a floating command menu, or a collapsible panel instead.
- **Missing whitespace.** Double the spacing. Let the design breathe. Dense layouts work for data dashboards, not for marketing pages.
- **Buttons not bottom-aligned in card groups.** When cards have different content lengths, CTAs end up at random heights. Pin buttons to the bottom of each card so they form a clean horizontal line regardless of content above.
- **Feature lists starting at different vertical positions.** In pricing tables or comparison cards, the list of features should start at the same Y position across all columns. Use consistent spacing above the list or fixed-height title/price blocks.
- **Inconsistent vertical rhythm in side-by-side elements.** When placing cards, columns, or panels next to each other, align shared elements (titles, descriptions, prices, buttons) across all items. Misaligned baselines make the layout look broken.
- **Mathematical alignment that looks optically wrong.** Centering by the math doesn't always look centered to the eye. Icons next to text, play buttons in circles, or text in buttons often need 1-2px optical adjustments to feel right.

### Interactivity and States

- **No hover states on buttons.** Add background shift, slight scale, or translate on hover.
- **No active/pressed feedback.** Add a subtle `scale(0.98)` or `translateY(1px)` on press to simulate a physical click.
- **Instant transitions with zero duration.** Add smooth transitions (200-300ms) to all interactive elements.
- **Missing focus ring.** Ensure visible focus indicators for keyboard navigation. This is an accessibility requirement, not optional.
- **No loading states.** Replace generic circular spinners with skeleton loaders that match the layout shape.
- **No empty states.** An empty dashboard showing nothing is a missed opportunity. Design a composed "getting started" view.
- **No error states.** Add clear, inline error messages for forms. Do not use `window.alert()`.
- **Dead links.** Buttons that link to `#`. Either link to real destinations or visually disable them.
- **No indication of current page in navigation.** Style the active nav link differently so users know where they are.
- **Scroll jumping.** Anchor clicks jump instantly. Add `scroll-behavior: smooth`.
- **Animations using `top`, `left`, `width`, `height`.** Switch to `transform` and `opacity` for GPU-accelerated, smooth animation.

### Content

- **Generic names like "John Doe" or "Jane Smith".** Use diverse, realistic-sounding names.
- **Fake round numbers like `99.99%`, `50%`, `$100.00`.** Use organic, messy data: `47.2%`, `$99.00`, `+1 (312) 847-1928`.
- **Placeholder company names like "Acme Corp", "Nexus", "SmartFlow".** Invent contextual, believable brand names.
- **AI copywriting cliches.** Never use "Elevate", "Seamless", "Unleash", "Next-Gen", "Game-changer", "Delve", "Tapestry", or "In the world of...". Write plain, specific language.
- **Exclamation marks in success messages.** Remove them. Be confident, not loud.
- **"Oops!" error messages.** Be direct: "Connection failed. Please try again."
- **Passive voice.** Use active voice: "We couldn't save your changes" instead of "Mistakes were made."
- **All blog post dates identical.** Randomize dates to appear real.
- **Same avatar image for multiple users.** Use unique assets for every distinct person.
- **Lorem Ipsum.** Never use placeholder latin text. Write real draft copy.
- **Title Case On Every Header.** Use sentence case instead.

### Component Patterns

- **Generic card look (border + shadow + white background).** Remove the border, or use only background color, or use only spacing. Cards should exist only when elevation communicates hierarchy.
- **Always one filled button + one ghost button.** Add text links or tertiary styles to reduce visual noise.
- **Pill-shaped "New" and "Beta" badges.** Try square badges, flags, or plain text labels.
- **Accordion FAQ sections.** Use a side-by-side list, searchable help, or inline progressive disclosure.
- **3-card carousel testimonials with dots.** Replace with a masonry wall, embedded social posts, or a single rotating quote.
- **Pricing table with 3 towers.** Highlight the recommended tier with color and emphasis, not just extra height.
- **Modals for everything.** Use inline editing, slide-over panels, or expandable sections instead of popups for simple actions.
- **Avatar circles exclusively.** Try squircles or rounded squares for a less generic look.
- **Light/dark toggle always a sun/moon switch.** Use a dropdown, system preference detection, or integrate it into settings.
- **Footer link farm with 4 columns.** Simplify. Focus on main navigational paths and legally required links.

### Iconography

- **Lucide or Feather icons exclusively.** These are the "default" AI icon choice. Use Phosphor, Heroicons, or a custom set for differentiation.
- **Rocketship for "Launch", shield for "Security".** Replace cliche metaphors with less obvious icons (bolt, fingerprint, spark, vault).
- **Inconsistent stroke widths across icons.** Audit all icons and standardize to one stroke weight.
- **Missing favicon.** Always include a branded favicon.
- **Stock "diverse team" photos.** Use real team photos, candid shots, or a consistent illustration style instead of uncanny stock imagery.

### Code Quality

- **Div soup.** Use semantic HTML: `<nav>`, `<main>`, `<article>`, `<aside>`, `<section>`.
- **Inline styles mixed with CSS classes.** Move all styling to the project's styling system.
- **Hardcoded pixel widths.** Use relative units (`%`, `rem`, `em`, `max-width`) for flexible layouts.
- **Missing alt text on images.** Describe image content for screen readers. Never leave `alt=""` or `alt="image"` on meaningful images.
- **Arbitrary z-index values like `9999`.** Establish a clean z-index scale in the theme/variables.
- **Commented-out dead code.** Remove all debug artifacts before shipping.
- **Import hallucinations.** Check that every import actually exists in `package.json` or the project dependencies.
- **Missing meta tags.** Add proper `<title>`, `description`, `og:image`, and social sharing meta tags.

### Strategic Omissions (What AI Typically Forgets)

- **No legal links.** Add privacy policy and terms of service links in the footer.
- **No "back" navigation.** Dead ends in user flows. Every page needs a way back.
- **No custom 404 page.** Design a helpful, branded "page not found" experience.
- **No form validation.** Add client-side validation for emails, required fields, and format checks.
- **No "skip to content" link.** Essential for keyboard users. Add a hidden skip-link.
- **No cookie consent.** If required by jurisdiction, add a compliant consent banner.

## Upgrade Techniques

When upgrading a project, pull from these high-impact techniques to replace generic patterns:

### Typography Upgrades
- **Variable font animation.** Interpolate weight or width on scroll or hover for text that feels alive.
- **Outlined-to-fill transitions.** Text starts as a stroke outline and fills with color on scroll entry or interaction.
- **Text mask reveals.** Large typography acting as a window to video or animated imagery behind it.

### Layout Upgrades
- **Broken grid / asymmetry.** Elements that deliberately ignore column structure — overlapping, bleeding off-screen, or offset with calculated randomness.
- **Whitespace maximization.** Aggressive use of negative space to force focus on a single element.
- **Parallax card stacks.** Sections that stick and physically stack over each other during scroll.
- **Split-screen scroll.** Two halves of the screen sliding in opposite directions.

### Motion Upgrades
- **Smooth scroll with inertia.** Decouple scrolling from browser defaults for a heavier, cinematic feel.
- **Staggered entry.** Elements cascade in with slight delays, combining Y-axis translation with opacity fade. Never mount everything at once.
- **Spring physics.** Replace linear easing with spring-based motion for a natural, weighty feel on all interactive elements.
- **Scroll-driven reveals.** Content entering through expanding masks, wipes, or draw-on SVG paths tied to scroll progress.

### Surface Upgrades
- **True glassmorphism.** Go beyond `backdrop-filter: blur`. Add a 1px inner border and a subtle inner shadow to simulate edge refraction.
- **Spotlight borders.** Card borders that illuminate dynamically under the cursor.
- **Grain and noise overlays.** A fixed, pointer-events-none overlay with subtle noise to break digital flatness.
- **Colored, tinted shadows.** Shadows that carry the hue of the background rather than using generic black.

## Fix Priority

Apply changes in this order for maximum visual impact with minimum risk:

1. **Font swap** — biggest instant improvement, lowest risk
2. **Color palette cleanup** — remove clashing or oversaturated colors
3. **Hover and active states** — makes the interface feel alive
4. **Layout and spacing** — proper grid, max-width, consistent padding
5. **Replace generic components** — swap cliche patterns for modern alternatives
6. **Add loading, empty, and error states** — makes it feel finished
7. **Polish typography scale and spacing** — the premium final touch

## Rules

- Work with the existing tech stack. Do not migrate frameworks or styling libraries.
- Do not break existing functionality. Test after every change.
- Before importing any new library, check the project's dependency file first.
- If the project uses Tailwind, check the version (v3 vs v4) before modifying config.
- If the project has no framework, use vanilla CSS.
- Keep changes reviewable and focused. Small, targeted improvements over big rewrites.
<!-- upstream:end -->
