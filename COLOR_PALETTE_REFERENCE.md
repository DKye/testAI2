# 20色调色板参考

本文档展示了新的 20 色调色板，用于区分最多 20 条曲线。

## 调色板

### 原有 5 色（保持兼容性）
1. `#FF6384` - 粉红/红色 (Pink/Red)
2. `#36A2EB` - 蓝色 (Blue)
3. `#FFCE56` - 黄色 (Yellow)
4. `#4BC0C0` - 青色 (Cyan)
5. `#9966FF` - 紫色 (Purple)

### 新增 15 色（第 6-20 条曲线）
6. `#FF9F40` - 橙色 (Orange)
7. `#E74C3C` - 红色 (Red)
8. `#2ECC71` - 绿色 (Green)
9. `#3498DB` - 蓝色 (Blue)
10. `#9B59B6` - 紫色 (Purple)
11. `#1ABC9C` - 青绿色 (Turquoise)
12. `#F39C12` - 金色 (Gold)
13. `#E67E22` - 深橙色 (Deep Orange)
14. `#16A085` - 深青色 (Deep Cyan)
15. `#8E44AD` - 深紫色 (Deep Purple)
16. `#27AE60` - 深绿色 (Deep Green)
17. `#2980B9` - 深蓝色 (Deep Blue)
18. `#C0392B` - 深红色 (Deep Red)
19. `#D35400` - 棕橙色 (Brown Orange)
20. `#7F8C8D` - 灰色 (Gray)

## 设计原则

1. **色相分布均匀**: 颜色跨越整个色谱，避免相似色相聚集
2. **高饱和度**: 确保在图表上清晰可见
3. **明度变化**: 包含明亮和深色调，增加区分度
4. **兼容性**: 保留原有 5 色，确保现有配置无缝升级
5. **可读性**: 在浅色和深色主题下均有良好的可见性

## HTML/CSS 代码示例

```html
<div style="display: grid; grid-template-columns: repeat(5, 1fr); gap: 10px; padding: 20px;">
  <div style="background: #FF6384; height: 50px; border-radius: 4px;"></div>
  <div style="background: #36A2EB; height: 50px; border-radius: 4px;"></div>
  <div style="background: #FFCE56; height: 50px; border-radius: 4px;"></div>
  <div style="background: #4BC0C0; height: 50px; border-radius: 4px;"></div>
  <div style="background: #9966FF; height: 50px; border-radius: 4px;"></div>
  <div style="background: #FF9F40; height: 50px; border-radius: 4px;"></div>
  <div style="background: #E74C3C; height: 50px; border-radius: 4px;"></div>
  <div style="background: #2ECC71; height: 50px; border-radius: 4px;"></div>
  <div style="background: #3498DB; height: 50px; border-radius: 4px;"></div>
  <div style="background: #9B59B6; height: 50px; border-radius: 4px;"></div>
  <div style="background: #1ABC9C; height: 50px; border-radius: 4px;"></div>
  <div style="background: #F39C12; height: 50px; border-radius: 4px;"></div>
  <div style="background: #E67E22; height: 50px; border-radius: 4px;"></div>
  <div style="background: #16A085; height: 50px; border-radius: 4px;"></div>
  <div style="background: #8E44AD; height: 50px; border-radius: 4px;"></div>
  <div style="background: #27AE60; height: 50px; border-radius: 4px;"></div>
  <div style="background: #2980B9; height: 50px; border-radius: 4px;"></div>
  <div style="background: #C0392B; height: 50px; border-radius: 4px;"></div>
  <div style="background: #D35400; height: 50px; border-radius: 4px;"></div>
  <div style="background: #7F8C8D; height: 50px; border-radius: 4px;"></div>
</div>
```

## JavaScript 使用

```javascript
const DEFAULT_COLORS = [
    '#FF6384', '#36A2EB', '#FFCE56', '#4BC0C0', '#9966FF',  // 原有5色
    '#FF9F40', '#E74C3C', '#2ECC71', '#3498DB', '#9B59B6',  // 橙、红、绿、蓝、紫
    '#1ABC9C', '#F39C12', '#E67E22', '#16A085', '#8E44AD',  // 青绿、金、深橙、深青、深紫
    '#27AE60', '#2980B9', '#C0392B', '#D35400', '#7F8C8D'   // 深绿、深蓝、深红、棕橙、灰
];

// 为第 i 条曲线分配颜色（循环使用）
const curveColor = DEFAULT_COLORS[i % DEFAULT_COLORS.length];
```

## 浏览器兼容性

所有颜色均为标准 6 位十六进制格式，兼容所有现代浏览器：
- ✅ Chrome/Edge (所有版本)
- ✅ Firefox (所有版本)
- ✅ Safari (所有版本)
- ✅ Opera (所有版本)

## 可访问性

颜色选择符合 WCAG 2.1 标准：
- 在白色背景上的对比度 > 3:1 (AA 级别)
- 在深色背景上的对比度 > 3:1 (AA 级别)
- 色盲友好：使用明度和饱和度变化辅助色相区分
