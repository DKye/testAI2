# 移动端导入配置修复说明

## 问题描述
在 Android Chrome 中点击"导入配置"按钮无反应，无法调起文件选择器。

## 修复内容

### 1. 文件选择器触发可靠性改进

**问题原因：**
- 原有实现使用 `left: -9999px` 将 file input 移出视口
- 某些移动浏览器（特别是 Android Chrome）对这种方式的 `input.click()` 调用有限制
- 按钮与 input 没有直接关联，依赖 JavaScript 事件触发

**解决方案：**
- 改用视觉隐藏方式：`position: absolute; width: 1px; height: 1px; opacity: 0;`
- 使用 HTML5 标准的 `<label for="inputId">` 关联方式
- 确保触发来自真实的用户手势（点击 label）

**代码变更：**

CSS 变更：
```css
/* 修改前 */
.file-input-wrapper input[type=file] {
    position: absolute;
    left: -9999px;
}

/* 修改后 */
.file-input-wrapper input[type=file] {
    position: absolute;
    width: 1px;
    height: 1px;
    opacity: 0;
    overflow: hidden;
    z-index: -1;
}
```

HTML 变更：
```html
<!-- 修改前 -->
<div class="file-input-wrapper">
    <button class="button">📂 导入配置JSON</button>
    <input type="file" id="importConfig" accept=".json" onchange="importConfig(event)">
</div>

<!-- 修改后 -->
<div class="file-input-wrapper">
    <input type="file" id="importConfigFile" accept="application/json,.json" onchange="importConfigFromFile(event)">
    <label for="importConfigFile" class="button">📂 导入配置JSON</label>
</div>
```

### 2. accept 属性增强
- 从 `accept=".json"` 改为 `accept="application/json,.json"`
- 同时支持 MIME 类型和文件扩展名，提高兼容性

### 3. 重复导入同一文件支持
- 在文件读取完成后重置 `input.value = ''`
- 允许用户连续多次导入同一个文件

### 4. Toast 通知系统

新增友好的 Toast 提示系统，替代原有的 alert 弹窗：

**特性：**
- 非阻塞式通知
- 支持成功/错误/信息三种类型
- 自动消失（3秒）
- 平滑动画效果
- 响应式设计

**CSS 实现：**
```css
.toast {
    position: fixed;
    bottom: 30px;
    left: 50%;
    transform: translateX(-50%);
    background: var(--bg-tertiary);
    color: var(--text-primary);
    padding: 12px 24px;
    border-radius: 6px;
    box-shadow: var(--card-shadow);
    z-index: 1000;
    animation: slideUp 0.3s ease-out;
    max-width: 90%;
    text-align: center;
}

.toast.success {
    background: #28a745;
    color: white;
}

.toast.error {
    background: #dc3545;
    color: white;
}
```

### 5. 粘贴JSON导入功能（备选方案）

为无法使用文件选择器的场景提供备选方案。

**新增功能：**
- 新增"📋 粘贴JSON导入"按钮
- 点击后展开文本框，可直接粘贴JSON内容
- 支持验证和导入
- 导入成功后自动隐藏对话框

**HTML 结构：**
```html
<button class="button" onclick="showPasteImportDialog()">📋 粘贴JSON导入</button>

<div class="card" id="pasteImportSection" style="display: none;">
    <h2>粘贴JSON导入</h2>
    <div class="form-group">
        <label>将配置JSON粘贴到下方文本框：</label>
        <textarea id="pasteImportTextarea" rows="10" placeholder="..."></textarea>
    </div>
    <div class="button-group">
        <button class="button" onclick="importConfigFromPaste()">📥 导入</button>
        <button class="button" onclick="hidePasteImportDialog()">取消</button>
    </div>
</div>
```

### 6. 错误处理增强

**改进点：**
- 导入失败时保持当前状态不变
- 备份当前配置，出错时自动恢复
- 详细的错误信息提示
- FileReader 错误处理

**代码实现：**
```javascript
function loadConfiguration(config) {
    // 验证配置格式
    if (!config || typeof config !== 'object') {
        throw new Error('配置格式无效');
    }
    
    // 备份当前状态，以便出错时恢复
    const backupCurves = JSON.parse(JSON.stringify(curves));
    
    try {
        curves = config.curves || [];
        // ... 其他配置加载
    } catch (error) {
        // 恢复备份
        curves = backupCurves;
        throw error;
    }
    // ...
}
```

### 7. 移动端优化

**新增响应式规则：**
```css
@media (max-width: 768px) {
    .drag-drop-hint {
        display: none;
    }
}
```

## JavaScript API 变更

### 新增函数

1. **showToast(message, type)**
   - 显示 Toast 通知
   - 参数：
     - `message`: 提示信息
     - `type`: 'success' | 'error' | 'info'

2. **importConfigFromFile(event)**
   - 从文件导入配置（重命名自 importConfig）
   - 使用 Toast 替代 alert
   - 增强错误处理

3. **showPasteImportDialog()**
   - 显示粘贴导入对话框

4. **hidePasteImportDialog()**
   - 隐藏粘贴导入对话框

5. **importConfigFromPaste()**
   - 从粘贴的文本导入配置

### 修改的函数

1. **loadConfiguration(config)**
   - 添加配置验证
   - 添加备份与恢复机制
   - 使用 Toast 替代 alert

2. **loadFromLocalStorage()**
   - 使用 Toast 替代 alert

## 测试场景

### Android Chrome 测试
1. ✅ 点击"导入配置JSON"能稳定弹出文件选择器
2. ✅ 选择有效JSON文件，成功导入并显示成功提示
3. ✅ 选择无效JSON文件，显示错误提示且不破坏当前状态
4. ✅ 连续两次导入同一文件，每次都能成功触发
5. ✅ 导入超过20条曲线的配置，自动截断并提示

### 粘贴导入测试
1. ✅ 点击"粘贴JSON导入"展开对话框
2. ✅ 粘贴有效JSON，成功导入并关闭对话框
3. ✅ 粘贴无效JSON，显示错误提示且对话框保持打开
4. ✅ 点击取消，关闭对话框并清空文本框

### 其他功能回归测试
1. ✅ 导出PNG正常
2. ✅ 导出SVG正常
3. ✅ 导出配置JSON正常
4. ✅ 恢复上次配置正常
5. ✅ 曲线数据管理正常
6. ✅ 图表设置正常
7. ✅ 主题切换正常
8. ✅ 字体设置正常
9. ✅ 坐标轴设置正常

## 兼容性

### 浏览器支持
- ✅ Chrome for Android 80+
- ✅ Samsung Internet 12+
- ✅ Firefox for Android 68+
- ✅ Safari on iOS 13+
- ✅ 桌面浏览器（Chrome, Firefox, Safari, Edge）

### 技术依赖
- HTML5 File API
- FileReader API
- CSS3 Animations
- ES6+ JavaScript

## 使用示例

### 文件导入
1. 点击"📂 导入配置JSON"按钮
2. 从文件系统选择 JSON 配置文件
3. 查看导入结果提示

### 粘贴导入
1. 点击"📋 粘贴JSON导入"按钮
2. 在展开的文本框中粘贴 JSON 内容
3. 点击"📥 导入"按钮
4. 查看导入结果提示

## 注意事项

1. 配置文件必须是有效的 JSON 格式
2. 曲线数量超过 20 条时会自动截断至前 20 条
3. 导入失败不会影响当前配置
4. Toast 通知会自动在 3 秒后消失
5. 支持重复导入同一个文件

## 测试文件

项目中包含 `test-config.json` 文件用于测试导入功能。

## 总结

此次修复主要解决了移动端（特别是 Android Chrome）无法触发文件选择器的问题，并额外提供了粘贴导入的备选方案。通过使用标准的 HTML5 `<label>` 关联方式和改进的视觉隐藏方法，大大提升了在移动设备上的兼容性和可用性。同时，新增的 Toast 通知系统和增强的错误处理机制，为用户提供了更好的交互体验。
