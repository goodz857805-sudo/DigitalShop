# DigitalShop · 数字化门店设计系统 Skill

海信 **数字化门店 / 服务指南针 PC 端 BI 后台**的页面生成与视觉优化 skill（内部代号 UEDMaster）。
给一句话需求，它负责读规范、取 token、按组件库拼页面、过自检；也负责把已有 demo 改得更好看。

- 版本：**0.7.0**
- 技术栈：**Arco Design 组件 + 覆盖 token**（不手写组件、不换组件库）
- 体积：约 **0.7 MB**，无 npm 依赖，纯 Markdown / CSS / SVG

---

## 一、这个 Skill 是什么

它有两种用法，靠需求关键词自动分流：

| 你的需求 | 它怎么做 | 执行依据 |
|---|---|---|
| 数字化门店、服务指南针的 BI 页面（报表、看板、指标卡、趋势分析） | 按设计规范生成页面，颜色 / 字号 / 间距 / 圆角 / 阴影全部从 token 取值 | `references/digital-store-spec.md` + `references/digital-store-tokens.css` |
| 没有设计系统的通用 demo、落地页 | 走通用审美审计（排版 / 配色 / 布局 / 交互状态 / 文案） | `SKILL.md` 末尾的上游清单 + `references/web-baseline.md` |

命中文档类需求时，它会自动处理这些事，**你不用指定**：

- **栅格节奏**：数字化门店 8px、服务指南针 / 一般 BI 报表 12px
- **开发模式**：说「QBI」用 QBI 规格（指标卡统一 252 宽、实心查询按钮）；说「自开发」用自开发规格；都没说默认自开发，并在交付说明里写一句假设
- **组件选型**：指标卡、趋势图、AI 辅助分析、筛选器、页签、侧导航、表格、标签，全部取组件库现成组件，不自造简化版
- **自检**：20 条交付清单 + 第 13 节 16 条硬规则（红好蓝差、品牌色分档、圆角只有 4/8、Tab 层级、控件高度等）
- **图标**：缺图标先在 `references/icons/` 的 492 枚里找，不凭记忆重画

它**不**做的事：不换技术栈、不重写已有项目、不修改上游正文（`SKILL.md` 里 `upstream:begin/end` 标记区逐字复用 MIT 上游内容）。

### 里面有什么

```text
DigitalShop/
├── SKILL.md                              # 入口：场景分流、硬约束、上游通用审计清单
├── README.md                             # 本文件
├── UPSTREAM.md                           # 上游来源、复用范围、哈希
├── LICENSE-upstream.txt                  # 上游 MIT 许可
└── references/
    ├── digital-store-spec.md             # 设计规范正文：74 章 / 64 个组件条目
    ├── digital-store-tokens.css          # 全部 token（颜色 / 字号 / 间距 / 圆角 / 阴影 / 层级）
    ├── guide.md                          # 说明文档四章：介绍 / 使用指南 / 通用版 / 更新日志
    ├── web-baseline.md                   # 交付底线：点击区域、移动端输入、状态不只靠颜色等
    ├── brand-color.md                    # 品牌强调色的对比度推导（仅通用场景）
    ├── color-mode-1.css                  # Figma 颜色模式导出
    ├── text-style-variables-mode-1.css   # Figma 文字样式导出
    ├── pt-container-head-default.svg     # AI 辅助分析头部底色
    ├── pt-container-title.svg            # AI 辅助分析渐变标题
    └── icons/                            # 图标库：9 分类 492 枚 SVG（≈0.5 MB）
        ├── arrow/        36
        ├── cover/        27
        ├── edit/         43
        ├── general/      90
        ├── interactive/  39
        ├── media/        16
        ├── supplement/   202
        ├── tips/         17
        └── trademark/    22
```

图标命名规则：`references/icons/<分类>/<名称>.svg`，小写 kebab-case，如 `references/icons/cover/star-fill.svg`。
默认填充 `#4E5969`（= `--text-color-text-2`），用时统一改成 `fill: currentColor` 跟随文字色。

---

## 二、怎么安装

### 方式一：项目级安装（推荐）

把整个 `DigitalShop` 目录放到目标项目的 skills 目录下：

```text
<你的项目>/
└── .qoder/
    └── skills/
        └── DigitalShop/
            ├── SKILL.md
            └── references/ ...
```

安装后**新开一个对话**，在 Skills 列表里能看到 `DigitalShop` 即生效。

注意事项：

- **目录名保持 `DigitalShop`**。Qoder 里显示的 skill 名就是目录名；`SKILL.md` frontmatter 里的 `name: ued-master` 是历史命名，两者不一致是正常的，不要只改一边。
- **不要改动 `SKILL.md` 的 frontmatter**（`name` / `description` 决定什么时候被命中）。
- `references/` 必须和 `SKILL.md` 一起放进去，缺了它会找不到规范与图标。

### 方式二：跨项目使用

把 `DigitalShop` 目录放进 Qoder 的用户级 skills 目录（在 Qoder 的 Skills 设置里能看到你本机的实际路径），这样所有项目都能命中。若不确定路径，也可以直接用 IDE 的 skill 创建 / 导入入口，指向这个目录。

### 方式三：打包分发给别人

1. 压缩 `DigitalShop/` 目录，**保证压缩包根目录就是 `SKILL.md`**（不要多套一层 `DigitalShop/` 文件夹）。
2. 确认包内包含 `references/icons/`（约 500 KB，492 个文件），这是唯一体积大头。
3. 对方解压到 `.qoder/skills/DigitalShop/` 即可。

### 升级

用新版本目录整体覆盖旧目录即可，无需迁移配置。版本变化看 `references/guide.md` 末尾的「更新日志」。

---

## 三、怎么用

### 1. 触发方式

自然语言说出场景即可，关键词命中就会加载：

- 数字化门店、服务指南针
- BI 后台、报表、看板、指标卡、数据可视化
- 或者直接点名：「用 DigitalShop 做个销售看板」

### 2. 最少要说清这几件事

说得越具体，澄清轮次越少：

1. **场景**：数字化门店 / 服务指南针 / 通用 demo
2. **开发模式**：自开发 / QBI（不确定就留给它判断，默认自开发）
3. **页面形态**：列表 / 指标卡仪表盘 / 趋势分析 / 详情 / 表单
4. **区块构成**：大标题、筛选器、指标卡行、图表、表格、AI 辅助分析
5. **筛选器控件**：日/周/月/年胶囊、日期范围、select、重置
6. **数据来源**：mock 还是真实接口
7. **特殊交互**：页签切换、Tab 层级、长页面冻结列、AI 辅助分析展开

**对比例子**

> 模糊：「做个销售报表页。」
>
> 明确：「做个数字化门店自开发模式的销售总览：大标题带日期说明、筛选器（日/周/月/年胶囊 + 日期范围 + 渠道/品类 select + 重置）、5 个指标卡、趋势图（实际 + 同比虚线）、底部 AI 辅助分析；数据先用 mock。」

### 3. 它的工作流程

1. 你给需求（一句话或一段）。
2. 它一次性追问关键模糊点，最多 1–2 轮。
3. 它先产出**界面结构说明**：页面框架、区块顺序、关键组件 / 取值 / 交互。
4. 你确认或直接改结构（增删区块、切换模式）。
5. 它按结构生成，并过自检后交付。

### 4. 常用请求 → 命中哪个页面模板

| 你要做的 | 模板 |
|---|---|
| 搭一个新的典型页 | 通用页面框架（Figma `5313:9671`） |
| 销售 / 核心指标看板，自开发 | 核心指标达成（`5313:9322`） |
| 服务备件类 QBI 报表 | 服务备件管理（`5313:8385`） |
| 行业份额、带一级 tab 与悬浮球的分析页，自开发 | 市场份额（`5313:9723`） |
| 门店全生命周期长页面（冻结列） | 门店生命周期（`5313:10682`） |

匹配度低时，它会按规范自主拼装——但仍然只用 Arco 组件 + token，不自造组件。

### 5. 想先看看长什么样

skill 包内没有网页，但 `references/digital-store-spec.md` 第 72 节写清了页面框架与 5 个模板的规格；如果你拿到的是完整仓库，根目录的 `digital-store-spec.html` 是可交互的可视化规范页，第 72 节的页面模板区可以点缩略图直接看完整页面（左下角「返回」或按 `Esc` 回目录）。

### 6. 交付时会告诉你什么

- 用了哪个模板、自开发还是 QBI，以及做了哪些判断假设
- 过了哪些自检项（20 条交付清单 + 第 13 节 16 条硬规则）
- **哪些没验**：没跑浏览器就明确写「未做浏览器实测」，不拿「没有报错」当「看起来对」

### 7. 常见问题

**Q：颜色能自己填吗？**
A：不行。颜色、字号、间距、圆角、阴影、层级一律从 `references/digital-store-tokens.css` 取，不另估。品牌绿分四档：`#00AAA6` 大色块、`#009093` 图标与边框、`#00757B` 链接文字与白字主按钮、`#F2FBFB` 轻量背景。

**Q：图标库没有我要的怎么办？**
A：先按 9 个分类在 `references/icons/` 里找；确实没有，再按 48 画布、填充路径（非 stroke）、`fill: currentColor` 的规则补一枚，并放进对应分类目录，保持命名 kebab-case。

**Q：为什么页面里的图表不能直接写 ECharts 配置就完事？**
A：页面只做拼装——趋势图取 `.tc`、AI 辅助分析取 `.u-ai`、指标卡取 `.ind`。组件改一次，页面和缩略图同步变；自己写一套会让 demo 与规范裂成两套外观。

**Q：只想要规范文档，不生成页面？**
A：直接说「按规范评审这个页面」或「解释第 9 节图标规则」，它会去读 `references/digital-store-spec.md` / `references/guide.md` 回答，不动你的代码。

---

## 四、版本与来源

| 项 | 值 |
|---|---|
| 版本 | 0.7.0 |
| 规范规模 | 74 章 / 64 个组件条目 / 5 个页面模板 / 492 枚图标 |
| 上游 | [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) `skills/redesign-skill`（MIT），复用范围与哈希见 `UPSTREAM.md` |
| 许可 | 上游正文 MIT，见 `LICENSE-upstream.txt`；本 skill 增补部分随本目录分发 |

改动规范时：**先改 `references/digital-store-spec.md` 与 `digital-store-tokens.css`，再改页面实现**，并在 `references/guide.md` 的更新日志补一条。页面里不写死色值与尺寸。
