# 区块库 · 可复制片段

`app-shell.html` 负责外壳，`components.css` 负责外观，本文件只给内容区的 DOM 与最小 JS。
**不要**在本文件基础上另写组件样式；缺什么先回 `components.css` 补，再回来改片段。

## 使用规则

1. 片段粘到 `app-shell.html` 的 `CONTENT START / END` 之间，替换掉 `.slot-note`。
2. 类名与嵌套层级照抄。页面里不出现 `.ind` / `.btn` / `.data-table` / `.u-ai` 的覆盖样式。
3. 网格里的指标卡加 `.is-fill`，让宽度跟着列走；单行固定卡可不加。
4. `data-mode` 只切筛选器与按钮形态；指标卡变体由内容决定，QBI 卡要显式加 `ind-qbi`。
5. 配色、字号、间距全部来自 `../digital-store-tokens.css`，片段里不写十六进制。

---

## 1. 指标卡

### 1.1 左右布局（自开发默认 232×108）

```html
<div class="ind">
  <div class="ind-in">
    <div class="ind-h">
      <span class="ind-t">门店总数</span>
      <span class="ind-ti" title="指标口径说明">
        <svg class="icon" viewBox="0 0 48 48" aria-hidden="true"><use href="#i-info"/></svg>
      </span>
    </div>
    <div class="ind-c">
      <div class="ind-total"><span class="ind-num">35,208</span><span class="ind-un">家</span></div>
      <div class="ind-d">
        <div class="ind-db">
          <span class="ind-dk">同比</span>
          <span class="ind-dv">
            <span class="ind-v2">+6.8%</span>
            <svg class="ind-trend up" viewBox="0 0 12 12" aria-hidden="true"><use href="#i-trend-up"/></svg>
          </span>
        </div>
      </div>
    </div>
  </div>
</div>
```

- 没有辅指标时整块 `.ind-d` 不写；没有口径说明时 `.ind-ti` 不写。
- 趋势图标只两枚：`#i-trend-up` 配 `.up`（红 `#F53F3F`）、`#i-trend-down` 配 `.down`（蓝 `#3491FA`）。
- 数字格式：千分位 + `tabular-nums`（`.ind-num` / `.ind-v2` 已经带）。

### 1.2 `indCard()` 生成器

需要循环出卡时用这一段，避免手抄结构：

```js
function trendSvg(dir){
  return '<svg class="ind-trend ' + dir + '" viewBox="0 0 12 12" aria-hidden="true"><use href="#i-trend-' + dir + '"/></svg>';
}
/* c = { t, n, u, rows:[[label, value, 'up'|'down'|'']] }
   opt = { qbi, vertical, fill, plain, compact, noInfo } */
function indCard(c, opt){
  opt = opt || {};
  var rows = (c.rows || []).map(function(r){
    return '<div class="ind-db"><span class="ind-dk">' + r[0] + '</span>'
      + '<span class="ind-dv"><span class="ind-v2">' + r[1] + '</span>'
      + (r[2] ? trendSvg(r[2]) : '') + '</span></div>';
  }).join('');
  var total = c.n ? '<div class="ind-total"><span class="ind-num">' + c.n + '</span>'
    + (c.u ? '<span class="ind-un">' + c.u + '</span>' : '') + '</div>' : '';
  var cls = 'ind' + (opt.qbi ? ' ind-qbi' : '') + (opt.vertical ? ' ind-v' : '')
    + (opt.fill ? ' is-fill' : '') + (opt.plain ? ' is-plain' : '') + (opt.compact ? ' is-compact' : '');
  return '<div class="' + cls + '"><div class="ind-in">'
    + '<div class="ind-h"><span class="ind-t">' + c.t + '</span>'
    + (opt.noInfo ? '' : '<span class="ind-ti" title="指标口径说明"><svg class="icon" viewBox="0 0 48 48" aria-hidden="true"><use href="#i-info"/></svg></span>')
    + '</div><div class="ind-c">' + total + (rows ? '<div class="ind-d">' + rows + '</div>' : '') + '</div>'
    + '</div></div>';
}
```

### 1.3 变体

| 追加类 | 尺寸 | 用途 |
|---|---|---|
| — | 232×108 | 自开发左右布局 |
| `.ind-v` | 237×108 | 自开发上下布局，数值与辅指标竖排 |
| `.ind-qbi` | 252×102 | QBI 左右布局，数字 24 号 |
| `.ind-qbi.ind-v` | 252×114 | QBI 上下布局 |
| `.is-fill` | 宽度自适应 | 放进 `.cards-4` / `.cards-6` 等网格时**必须加** |
| `.is-plain` | 宽度跟随容器 | 辅指标区宽 120，数值与辅指标竖排 |
| `.is-compact` | 123×74 | 小卡，无辅指标，仅标题 + 24 号数值 |
| `.is-xs` | 197 宽 | 联动指标卡，辅指标区宽 120 |
| `.is-link` | 尺寸不变 | 联动选中底 `#F7F8FA` |
| `.is-off` | 尺寸不变 | 联动未选中 `opacity:.5`，只用于 QBI 联动态 |

`.is-fill` 必须写在固定宽度类之后（CSS 已保证流体优先），页面上正常写法如
`<div class="ind ind-qbi ind-v is-fill">`。

### 1.4 联动态（QBI 专有）

```html
<div class="ind ind-qbi is-link">…</div>   <!-- 选中 -->
<div class="ind ind-qbi is-off">…</div>    <!-- 未选中 -->
```

自开发没有联动态，不要给 `.ind` 加 `is-link` / `is-off`。

### 1.5 容器

```html
<div class="panel">
  <div class="panel-h"><b>门店概况</b></div>
  <div class="panel-body">
    <div class="cards cards-4" id="ovCards"></div>
  </div>
</div>
```

- `.cards-4` / `.cards-6` 的列宽是 `1fr`，卡宽不固定，因此卡要 `.is-fill`。
- 卡片区与图表、表格之间的间距由 `.panel-body` 的 `var(--page-gap)` 统一给，不另加 margin。
- 页面节奏：`data-scene="store"` → 8px，`"compass"` → 12px，片段不用关心。

---

## 2. 图表卡

### 2.1 结构

```html
<div class="panel">
  <div class="panel-h"><b>门店数量与动销率</b></div>
  <div class="panel-body">
    <div class="chart h280" id="chart1" role="img" aria-label="门店数量与门店动销率趋势"></div>
  </div>
</div>

<!-- 一行两图 -->
<div class="panel">
  <div class="panel-h"><b>趋势对比</b></div>
  <div class="panel-body">
    <div class="chart-row">
      <div class="chart h280" id="chart2" role="img" aria-label="门店数量趋势"></div>
      <div class="chart h280" id="chart3" role="img" aria-label="动销率趋势"></div>
    </div>
  </div>
</div>
```

高度只用这几档，不写行内高度：

| 类 | 高度 | 场景 |
|---|---|---|
| `.chart` | 260 | 默认单图 |
| `.chart.h236` | 236 | 紧凑行内图 |
| `.chart.h280` | 280 | 双图并排 |
| `.chart.h300` | 300 | 备件类长图 |
| `.chart.h338` | 338 | 堆叠柱 |
| `.chart.h342` | 342 | 环形 / 多系列折线 |

图表实例按容器实测尺寸绘制；外层宽度变化由 ECharts `resize()` 兜底，不用固定 viewBox 拉伸。

### 2.2 ECharts 基础 option

先引库（沿用 demo 的本地依赖）：

```html
<script src="assets/echarts.min.js"></script>
```

```js
function cssVar(n){ return getComputedStyle(document.documentElement).getPropertyValue(n).trim(); }
function axisBase(){
  return {
    grid:{ left:4, right:8, top:30, bottom:0, containLabel:true },
    legend:{ top:0, left:'center', itemWidth:8, itemHeight:8, itemGap:24, icon:'roundRect',
      itemStyle:{ borderWidth:0, borderRadius:2 },
      textStyle:{ color:cssVar('--text-color-text-1'), fontSize:12 } },
    tooltip:{ trigger:'axis', backgroundColor:'#fff', borderColor:cssVar('--chart-grid'), borderWidth:1,
      textStyle:{ color:cssVar('--text-color-text-1'), fontSize:12 } },
    xAxis:{ type:'category', boundaryGap:false,
      axisLine:{ lineStyle:{ color:cssVar('--chart-axis') } }, axisTick:{ show:false },
      axisLabel:{ color:cssVar('--text-color-text-2'), fontSize:12 } },
    yAxis:{ type:'value', axisLine:{ show:false }, axisTick:{ show:false },
      axisLabel:{ color:cssVar('--text-color-text-2'), fontSize:12 },
      splitLine:{ lineStyle:{ color:cssVar('--chart-grid'), type:'dashed' } } }
  };
}
```

系列：折线 `smooth:false`、`symbol:'circle'`、`symbolSize:4`、`lineStyle.width:2`；柱 `barWidth:20`、`borderRadius:[2,2,0,0]`；堆叠只有最上一层圆角。同比 / 环比用 `--businesssemantic-bs-sameperiod`，多类别才按 `--chart-chart-1…6` 顺序取色。

### 2.3 挂载与缩放

```js
var charts = [];
var hasECharts = typeof window.echarts !== 'undefined';

function mount(id, option){
  var el = document.getElementById(id);
  if(!el) return null;
  if(!hasECharts){
    el.classList.add('is-fallback');
    el.textContent = 'ECharts 未加载，图表占位';
    return null;
  }
  var inst = echarts.getInstanceByDom(el) || echarts.init(el);
  inst.setOption(option, true);
  charts.push(inst);
  return inst;
}

var rt;
window.addEventListener('resize', function(){
  clearTimeout(rt);
  rt = setTimeout(function(){
    charts.forEach(function(c){ if(!c.isDisposed()) c.resize(); });
  }, 140);
});
```

重渲染前先 `dispose` 同前缀实例，避免内存泄漏：

```js
function disposeCharts(prefix){
  charts = charts.filter(function(c){
    if(prefix && c.getDom().id.indexOf(prefix) !== 0) return true;
    c.dispose();
    return false;
  });
}
```

### 2.4 图表注意

- 折线是直线，不开启 `smooth`。
- 网格只画横向虚线（`splitLine.type:'dashed'`），纵向不画。
- 图例最多 3 项，色块 8×8、居中、无描边。
- 双轴只给左轴网格，右轴不画 `splitLine`。
- 环形图中心只放百分比，图例放底部。
- 柱状图不给每个系列换色，同义系列用同一色相深浅。

---

## 3. 表格

```html
<div class="panel">
  <div class="panel-h">
    <b>门店明细</b>
    <span class="tag tag-gray">共 35,208 家</span>
  </div>
  <div class="tbl-wrap">
    <table class="data-table is-frozen">
      <colgroup>
        <col style="width:78px"><col style="width:110px"><col style="width:196px">
        <col style="width:146px"><col style="width:116px"><col style="width:116px">
      </colgroup>
      <thead>
        <tr>
          <th>门店编码</th><th>大区</th><th>门店名称</th>
          <th>月均单店产出</th><th class="n">销售额</th><th class="n">达成率</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>SH-0218</td><td>华东</td>
          <td><span class="cell-name" title="青岛香港中路旗舰店">青岛香港中路旗舰店</span></td>
          <td>16.4</td><td class="n">158.6 万</td>
          <td class="n is-up">104.2%</td>
        </tr>
      </tbody>
      <tfoot>
        <tr class="is-total">
          <td>合计</td><td>—</td><td>10 家门店</td>
          <td>—</td><td class="n">1,286.4 万</td><td class="n is-up">101.7%</td>
        </tr>
      </tfoot>
    </table>
  </div>
</div>
```

- 数字列统一 `class="n"`：右对齐 + `tabular-nums`，表头和单元格都要加。
- 好坏用 `.is-up`（红）/ `.is-down`（蓝），不要用品牌绿表达涨跌。
- 长名称用 `.cell-name` 省略，`title` 里放全称。
- `.tbl-wrap` 自带外框、4 圆角、横向滚动；表格 `min-width` 已锁 1180，列多时滚动而不是压缩。
- `.is-frozen` 冻结首列（底色 + 右侧分隔线已处理）。
- 合计行用 `<tfoot><tr class="is-total">`，不是普通 `tbody` 行。
- 需要分页时在 `.tbl-wrap` 下方另起一行放 Arco `Pagination`；本模板不含分页样式。

---

## 4. AI 辅助分析

```html
<section class="u-ai" aria-label="AI 辅助分析">
  <div class="u-ai-hd">
    <svg class="u-ai-bg" viewBox="0 0 1200 48" preserveAspectRatio="none" aria-hidden="true"><use href="#i-ai-head"/></svg>
    <div class="u-ai-t">
      <svg viewBox="0 0 18 16" aria-hidden="true"><use href="#i-ai"/></svg>
      <span>AI辅助分析</span>
    </div>
    <div class="u-ai-tools">
      <button class="u-ai-btn" type="button" data-ai-all>一键展开</button>
    </div>
  </div>
  <div class="u-ai-bd">
    <div class="u-ai-row" data-ai-row>
      <div class="u-ai-it">
        <span class="u-ai-tag is-c1">机会</span>
        <p class="u-ai-tx">华东 12 家门店的动销率高于大盘，<span class="u-ai-up">+3.6pp</span>，可优先复制其陈列方案。</p>
        <button class="u-ai-more" type="button" data-ai-more aria-expanded="false" aria-label="展开下钻">
          <svg viewBox="0 0 48 48" aria-hidden="true"><use href="#i-chev"/></svg>
        </button>
      </div>
      <div class="u-ai-dtl">
        <div class="u-ai-it">
          <span class="u-ai-tag is-c2">下钻</span>
          <p class="u-ai-tx">青岛香港中路店动销率 96.8%，高于同类门店 4.1pp。</p>
        </div>
      </div>
    </div>
  </div>
</section>
```

- 标签色阶：`.is-c1` 品牌、`.is-c2` 信息、`.is-c3` 警示、`.is-c4` 紫、`.is-c5` 成功，按语义选。
- 涨跌文字只用 `.u-ai-up` / `.u-ai-down`。
- 头部两张渐变图来自 sprite 的 `#i-ai-head` / `#i-ai`；复制页面时 sprite 整段带走。
- 展开逻辑：行加 `.is-open`，按钮同步 `aria-expanded`；一键展开按钮文案在「一键展开 / 全部收起」之间切。

```js
document.querySelectorAll('[data-ai-more]').forEach(function(btn){
  btn.addEventListener('click', function(){
    var row = btn.closest('[data-ai-row]');
    var open = !row.classList.contains('is-open');
    row.classList.toggle('is-open', open);
    btn.setAttribute('aria-expanded', String(open));
  });
});
```

---

## 5. 区块级页签与标签

### 5.1 一级 tab（面板内）

```html
<div class="tabs-l1" role="tablist" aria-label="生命周期阶段">
  <span class="tl1 is-active" role="tab" aria-selected="true" data-tab="new">新建店</span>
  <span class="tl1" role="tab" aria-selected="false" data-tab="keep">结存店</span>
</div>
```

切页签时同步 `is-active` 与 `aria-selected`，只重渲染该面板内容，不重建外壳。

### 5.2 标签

```html
<span class="tag tag-gray">共 35,208 家</span>
<span class="tag tag-primary">已达标</span>
<span class="tag tag-success">正常</span>
<span class="tag tag-warning">预警</span>
<span class="tag tag-danger">异常</span>
<span class="tag tag-info">信息</span>
<span class="tag tag-purple">标签</span>
```

高度 22、圆角 4、12 号字，不带描边。

---

## 6. 与外壳联动

外壳在筛选变化时派发事件，内容区监听即可，不要直接读筛选控件 DOM：

```js
document.addEventListener('ds:filterchange', function(e){
  var f = e.detail;   // { scene, mode, period, month, filters }
  // 按 f.period / f.month / f.filters 重新取数并渲染内容区
});

document.addEventListener('ds:action', function(e){
  if(e.detail.id === 'btnReset'){ /* 重置后的本地状态 */ }
});
```

随时取当前筛选值：

```js
var f = window.DSShell.getFilter();
```

提示用外壳的 toast，不调 `alert`：

```js
window.DSShell.toast('已按「华东」筛选（演示数据）');
```

首屏渲染顺序：先渲染静态内容 → `mount()` 图表 → 再挂 `ds:filterchange` 监听。不要在外壳初始化前读筛选值。

---

## 7. 交付前自检

1. 网格里的卡都有 `.is-fill`，窄窗口不横向溢出。
2. 指标卡没有行内宽高，QBI 卡显式带 `ind-qbi`。
3. 涨跌只用红 / 蓝，没有用品牌绿表达好坏。
4. 图表折线是直线、网格只有横向虚线、图例 ≤3 项且 8×8。
5. 图表高度取自 `.h236/.h280/.h300/.h338/.h342`，没有行内 `style="height:…"`。
6. 数字列都带 `.n`，千分位与 `tabular-nums` 生效。
7. 表格首列冻结、横向滚动正常，底部合计行是 `tfoot.is-total`。
8. AI 行展开 / 收起同步 `aria-expanded`，渐变头图与渐变标题都在。
9. 所有图标来自 sprite（`#i-*`），没有 emoji、iconfont 或临时手绘路径。
10. 页面没有覆盖 `.ind` / `.btn` / `.data-table` / `.u-ai` 的样式。
11. 窗口 1440 / 1024 / 760 / 390 四档无横向滚动（表格自身滚动除外），内容区超出时竖向滚动。
12. 筛选切换后内容确实重渲染，提示走 toast。
