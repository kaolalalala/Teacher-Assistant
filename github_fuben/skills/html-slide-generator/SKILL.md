---
name: "html-slide-generator"
description: "Generates HTML slide presentations from markdown outlines. Invoke when user requests creating HTML slides, presentation decks, or converting documents to slide format."
---

# HTML 幻灯片生成器技能

## 技能概述

本技能用于将 Markdown 格式的大纲文档转换为完整的 HTML 幻灯片演示系统。采用类似 PowerPoint 的全屏展示模式，**移除所有导航按钮**，确保单页内容无需滚动即可完整显示，提供简洁专业的演示体验。

## 适用场景

当用户提出以下需求时，应调用此技能：
- "基于这个大纲制作 HTML 幻灯片"
- "帮我创建一套演示文稿"
- "把这个文档转换成幻灯片格式"
- "生成 HTML 演示页面"
- 需要将教学内容、报告材料、培训文档等转换为可交互的 HTML 幻灯片

## 执行流程

### 内容分析与规划

1. **读取大纲文件**
   - 使用 `Read` 工具读取用户提供的 Markdown 大纲文件
   - 分析文档结构、主题、章节层次

2. **幻灯片页面规划**
   - 根据大纲内容划分适当的幻灯片页数（通常 15-25 页）
   - 为每页分配内容主题：
     - 封面页（1 页）：标题、副标题、版本信息
     - 目录页（1 页）：内容导航
     - 内容页（若干）：按大纲章节分配
     - 总结页（1 页）：结语、建议、资源

3. **创建规划文档**
   - 在 `markdown/` 目录下为每页创建 `.md` 规划文件
   - 每个规划文件包含：
     - 版面布局说明
     - 内容规划（标题、正文、重点概念、金句、练习题等）
     - 视觉元素建议

### 目录结构创建

4. **创建文件夹结构**
   ```
   slides/
   ├── index.html          # 主入口文件
   ├── css/
   │   └── style.css      # 统一样式表
   ├── js/
   │   └── slides.js      # 交互功能脚本
   ├── markdown/          # 页面规划文档
   │   ├── page01.md
   │   └── ...
   └── pages/             # 幻灯片 HTML 页面
       ├── slide01.html
       └── ...
   ```

   使用 PowerShell 命令：
   ```powershell
   New-Item -ItemType Directory -Force -Path <路径列表>
   ```

### 样式与脚本实现

5. **创建 CSS 样式表** (`css/style.css`)
   
   **核心样式组件（PPT 风格优化）**：
   - 全局重置与渐变背景
   - 幻灯片容器（16:9 比例，最大宽度 1200px，**固定高度无滚动**）
   - 封面页特殊样式（居中、渐变背景）
   - 内容框样式：
     - `.concept-box`：概念框（浅蓝渐变，左侧边框）
     - `.quote-box`：金句框（浅黄背景，斜体）
     - `.insight-box`：洞察框（绿色渐变，带灯泡图标）
     - `.exercise-box`：练习框（蓝色边框，带铅笔图标）
   - 数学公式样式 `.math-formula`
   - 对比表格 `.comparison-table`
   - 图示区域 `.diagram-container`
   - **移除导航按钮样式**（`.nav-btn` 不再需要）
   - **移除进度条样式**（`.progress-bar` 不再需要）
   - **移除页码显示**（`.page-number` 不再需要）
   - 目录页 `.toc-list`
   - 响应式媒体查询
   - 打印样式优化
   
   **关键 CSS 修改**：
   ```css
   /* 固定高度，禁止滚动 */
   .slide {
       overflow: hidden;  /* 禁止内容溢出 */
   }
   
   .slide-content {
       overflow: hidden;  /* 禁止滚动 */
       flex: 1;
       display: flex;
       flex-direction: column;
       justify-content: center;  /* 内容垂直居中 */
   }
   
   /* 移除导航元素 */
   .navigation,
   .page-number,
   .progress-bar {
       display: none;  /* 完全隐藏 */
   }
   ```

6. **创建 JavaScript 控制器** (`js/slides.js`)
   
   **简化版 SlideController 类功能**：
   - `constructor()`: 初始化幻灯片列表、当前索引
   - `showSlide(index)`: 显示指定页面
   - `nextSlide()` / `prevSlide()`: 页面切换
   - **移除** `updateButtons()`（无需按钮状态管理）
   - **移除** `updatePageNumber()`（无需页码）
   - **移除** `updateProgressBar()`（无需进度条）
   - `bindKeyboardEvents()`: 绑定键盘事件（仅保留方向键）
     - → / ↓：下一页
     - ← / ↑：上一页
   - `goToSlide(index)`: 从目录跳转

### HTML 页面生成

7. **创建封面页** (`pages/slide01.html`)
   - 使用 `.cover-slide` 类
   - 大标题（48px，白色）
   - 副标题（24px，浅灰）
   - 版本信息
   - 渐变紫色背景

8. **创建目录页** (`pages/slide03.html`)
   - 使用 `.toc-slide` 类
   - 可点击的目录项列表
   - 每项包含 `data-slide-index` 属性
   - 悬停效果（右移 + 阴影）

9. **创建内容页** (`pages/slide04.html` - `slide19.html`)
   
   **页面结构模板（PPT 风格 - 无导航）**：
   ```html
   <!DOCTYPE html>
   <html lang="zh-CN">
   <head>
       <meta charset="UTF-8">
       <title>页面标题</title>
       <link rel="stylesheet" href="../css/style.css">
   </head>
   <body>
       <div class="slide-container">
           <div class="slide active" id="slideXX">
               <h1 class="slide-title">主标题</h1>
               <h2 class="slide-subtitle">副标题（可选）</h2>
               <div class="slide-content">
                   <!-- 内容框：concept-box / quote-box / insight-box / exercise-box -->
                   <!-- 数学公式：math-formula -->
                   <!-- 表格：comparison-table -->
                   <!-- 图示：diagram-container -->
               </div>
               <!-- 移除导航按钮、页码、进度条 -->
           </div>
       </div>
       <script src="../js/slides.js"></script>
   </body>
   </html>
   ```

10. **创建总结页** (`pages/slide20.html`)
    - 学习建议列表
    - 结语引用框（渐变背景）
    - 推荐资源
    - **移除进度条**

### 入口文件创建

11. **创建主入口** (`index.html`)
    
    **关键组件（PPT 风格简化版）**：
    - Header：大标题、副标题
    - iframe 容器：加载幻灯片页面
    - **移除页码显示**
    - **移除控制按钮**（首页、上一页、下一页、末页）
    - **移除 Footer 使用说明**
    - 内联 JavaScript：
      - `currentSlide` 状态管理
      - `updateSlide()` 切换 iframe 源
      - **简化键盘事件监听**（仅方向键）
      - **移除按钮状态更新**

### 测试与验证

12. **启动本地服务器**
    ```powershell
    cd <slides 目录>
    python -m http.server 8080
    ```

13. **打开预览**
    - 使用 `OpenPreview` 工具打开 http://localhost:8080
    - 验证所有页面正常显示
    - **测试键盘方向键控制**（← →）
    - **确认无导航按钮、无进度条、无页码**
    - 确认内容无需滚动即可完整显示

## 关键参数配置

### 样式参数
| 参数 | 默认值 | 说明 |
|------|--------|------|
| 主题色 | `#667eea` → `#764ba2` | 渐变紫色主题 |
| 容器宽度 | `1200px` | 最大宽度 |
| 宽高比 | `16/9` | 幻灯片比例 |
| 字体 | `Microsoft YaHei` | 中文字体 |
| 标题大小 | `32px` | 内容页标题 |
| 封面标题 | `48px` | 封面页标题 |
| **内容区** | `overflow: hidden` | **禁止滚动** |
| **导航元素** | `display: none` | **完全隐藏** |

### 内容规划参数
| 参数 | 建议值 | 说明 |
|------|--------|------|
| 总页数 | 15-25 页 | 根据大纲内容调整 |
| 每页内容量 | 3-5 个要点 | 避免信息过载 |
| 练习页比例 | 每章节 1 页 | 巩固学习 |
| 视觉元素 | 每页 1-2 个框 | 增强可读性 |

## 内容框使用指南

### Concept Box（概念框）
**用途**：展示核心定义、知识点、对比信息
```html
<div class="concept-box">
    <h3>📖 标题</h3>
    <ul>
        <li>要点 1</li>
        <li>要点 2</li>
    </ul>
</div>
```

### Quote Box（金句框）
**用途**：强调重要说明、编者按、引用内容
```html
<div class="quote-box">
    <strong>💡 提示</strong><br><br>
    重要内容...
</div>
```

### Insight Box（洞察框）
**用途**：展示技巧、策略、核心洞察
```html
<div class="insight-box">
    <h4>💡 标题</h4>
    <p>内容...</p>
</div>
```

### Exercise Box（练习框）
**用途**：放置练习题、实战演练
```html
<div class="exercise-box">
    <h4>✏️ 标题</h4>
    <div class="question">
        <strong>1.</strong> 题目内容...
    </div>
</div>
```

## 复用条件

本技能适用于以下场景：
1. ✅ 用户有结构化的 Markdown 大纲文档
2. ✅ 需要创建用于演示、教学、培训的幻灯片
3. ✅ 要求 HTML 格式，可在浏览器中运行
4. ✅ **需要类似 PPT 的简洁演示效果（无导航按钮、无滚动）**
5. ✅ 希望统一视觉风格、响应式设计

**不适用场景**：
- ❌ 需要复杂动画效果（如 Reveal.js、Impress.js）
- ❌ 需要在线协作编辑
- ❌ 需要导出为 PPT/PDF 格式（虽有打印样式但非主要功能）
- ❌ 单页内容极多（超过 1000 字）
- ❌ **需要保留导航按钮、进度条、页码显示**

## 最佳实践

### 内容组织
1. **层次清晰**：每页一个主题，避免信息混杂
2. **视觉分离**：不同类型内容使用不同框样式
3. **适度留白**：不要填满整个页面
4. **重点突出**：使用颜色、图标强调关键信息

### 技术实现
1. **语义化 HTML**：使用正确的标签（h1, h2, p, ul, li）
2. **CSS 类命名**：遵循 BEM 或类似规范
3. **JavaScript 模块化**：使用类封装功能
4. **响应式优先**：确保移动端可读

### 性能优化
1. **静态资源**：CSS/JS 文件缓存
2. **iframe 懒加载**：按需加载页面
3. **最小化 DOM**：避免过深的嵌套
4. **CSS 动画**：使用 transform 而非 position

## 扩展建议

### 可添加功能
- [ ] 全屏模式切换
- [ ] 幻灯片缩放
- [ ] 目录侧边栏
- [ ] 搜索功能
- [ ] 主题切换（深色模式）
- [ ] 导出 PDF 功能
- [ ] 演讲者备注
- [ ] 自动播放模式

### 样式变体
- 商务蓝主题
- 教育绿主题
- 科技暗黑主题
- 简约白色主题

## 故障排查

| 问题 | 可能原因 | 解决方案 |
|------|----------|----------|
| 幻灯片不显示 | JavaScript 未加载 | 检查 script 标签路径 |
| 样式不生效 | CSS 路径错误 | 使用相对路径 `../css/style.css` |
| 键盘控制无效 | 事件未绑定 | 确认 `bindKeyboardEvents()` 被调用 |
| 进度条不动 | 宽度未更新 | 检查 `updateProgressBar()` 计算 |
| iframe 空白 | 页面路径错误 | 验证 `pages/slideXX.html` 存在 |

## 版本历史

- **v2.0** (2026-02-28): PPT 风格优化版本
  - ✅ **移除所有导航按钮**（上一页、下一页等）
  - ✅ **移除进度条和页码显示**
  - ✅ **固定内容区域，禁止滚动**
  - ✅ **简化键盘控制**（仅保留方向键）
  - ✅ **类似 PowerPoint 的简洁演示效果**

- **v1.0** (2026-02-28): 初始版本，包含完整的幻灯片生成流程
  - iframe 静态页面模式
  - 20 页标准模板
  - 渐变紫色主题
  - 键盘控制支持
  - 进度条与页码显示

## 相关技能

- `markdown-to-html`: Markdown 转 HTML 基础转换
- `css-theme-designer`: CSS 主题设计
- `interactive-js-builder`: 交互功能构建
