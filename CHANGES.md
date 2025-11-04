# 最大效率点标记与表格高亮 - 实现说明

## 变更概览

本次优化针对功率-效率曲线分析工具的最大效率点显示和交互进行了全面改进。

## 主要变更

### 1. CSS样式增强 (lines 263-281)

#### 新增样式类 `max-efficiency-row`
- 为数据表中的最大效率点行添加金色背景高亮
- 左侧添加3px金色边框作为视觉标识
- 行首自动添加星星图标 (⭐)
- 文字加粗以增强可读性
- 深色模式下自动调整透明度以保证良好的对比度

```css
.data-table tr.max-efficiency-row {
    background: rgba(255, 215, 0, 0.25);
    font-weight: 600;
    border-left: 3px solid #FFD700;
}

[data-theme="dark"] .data-table tr.max-efficiency-row {
    background: rgba(255, 215, 0, 0.15);
}
```

### 2. 用户界面文本优化 (line 421, 508)

- 将"高亮最大效率点"改为"标记最大效率点"，更准确地描述功能
- 更新使用说明，明确说明并列最大值时选择功率最小的点

### 3. 图例过滤 (lines 846-849)

添加图例过滤器，移除最大效率点的独立图例项：

```javascript
legend: {
    display: true,
    labels: {
        color: getComputedStyle(document.body).getPropertyValue('--text-primary'),
        filter: function(item, chart) {
            // 不显示最大效率点的图例项
            return !item.text.includes('最大效率');
        }
    }
}
```

### 4. 最大效率点标记样式优化 (lines 949-974)

重新设计最大效率点在图表上的显示方式：

- **颜色方案**：核心使用曲线颜色，边框使用对比色（深色模式白色，浅色模式黑色）
- **大小**：半径9px，悬停时11px
- **形状**：圆形标记点
- **层级**：order: -1 确保标记显示在最上层

```javascript
// 使用白色边框增强可见性
const isDarkMode = document.documentElement.getAttribute('data-theme') === 'dark';
const contrastBorder = isDarkMode ? '#ffffff' : '#000000';

datasets.push({
    label: `${curve.label} - 最大效率`,
    data: [maxPoint],
    borderColor: contrastBorder,
    backgroundColor: curve.color,
    borderWidth: 2,
    pointRadius: 9,
    pointHoverRadius: 11,
    pointStyle: 'circle',
    showLine: false,
    order: -1
});
```

### 5. 数据表高亮逻辑 (lines 704-730)

在 `renderDataTable` 函数中实现自动高亮：

```javascript
// 找出最大效率点
const curve = curves[curveIndex];
const maxPoint = findMaxEfficiencyPoint(curve);

// 判断是否为最大效率点
const isMaxPoint = maxPoint && point.x === maxPoint.x && point.y === maxPoint.y;
const rowClass = isMaxPoint ? ' class="max-efficiency-row"' : '';
```

## 技术特性

### 自动更新机制
所有数据变化操作都会触发表格重新渲染和图表更新：
- `parseBulkData()` - 批量粘贴
- `addPoint()` - 添加单点
- `removePoint()` - 删除点
- `clearCurveData()` - 清空数据
- `updateCurveColor()` - 颜色变更

每次都会调用 `renderCurves()` 和 `updateChart()`，确保高亮和标记实时同步。

### 曲线显隐行为
- **隐藏曲线**：图表上不显示最大效率点标记（`curve.visible` 检查）
- **数据表**：仍然显示高亮，不受 `visible` 状态影响

### 并列最大值处理
`findMaxEfficiencyPoint` 函数实现逻辑：
```javascript
if (point.y > maxPoint.y || (point.y === maxPoint.y && point.x < maxPoint.x)) {
    maxPoint = point;
}
```
当效率值相同时，自动选择功率（X值）较小的点。

### 状态持久化
开关状态通过以下机制保存：
- `getCurrentConfiguration()` - 导出时包含 `highlightMaxEfficiency`
- `loadConfiguration()` - 导入时恢复设置
- `saveToLocalStorage()` - 自动保存到浏览器本地存储

## 兼容性

### 主题切换
- CSS使用 `[data-theme="dark"]` 选择器适配深色模式
- JavaScript动态检测主题状态调整标记边框颜色

### 现有功能
所有变更均为增量式改进，不影响：
- PNG/SVG 导出
- 参考线功能
- 平滑曲线
- 区域填充
- 坐标轴设置
- 最大效率汇总表

## 验收要点

1. ✅ 图例中不显示"最大效率点"单独条目
2. ✅ 图上标记使用曲线同色（核心）+ 对比色边框
3. ✅ 开关控制标记显示/隐藏
4. ✅ 数据表自动高亮最大效率点行
5. ✅ 数据变化时高亮自动更新
6. ✅ 并列最大值时选功率最小点
7. ✅ 隐藏曲线不影响表格高亮
8. ✅ 导出/导入保存开关状态
9. ✅ localStorage持久化
10. ✅ 深色/浅色主题适配

## 文件变更

仅修改 `/home/engine/project/index.html`：
- 新增 CSS 样式规则 (~20 行)
- 修改 JavaScript 逻辑 (~30 行)
- 更新用户界面文本 (~5 行)

总计约 55 行代码变更，保持单文件架构不变。
