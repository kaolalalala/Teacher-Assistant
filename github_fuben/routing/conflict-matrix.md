# Skill 冲突矩阵

本文档定义多个 skill 可能同时命中时的裁决规则。

---

## 一、冲突裁决原则

1. **控制类优先**：控制类 skill 一律优先于业务类 skill
2. **组合优先于单技能**：组合型请求优先路由到 workflow-pack
3. **明确优先于模糊**：用户明确指定时，以用户指定为准
4. **后处理不抢首轮**：后处理类 skill 不得抢占首轮主路由

---

## 二、冲突矩阵表

### lesson-planner vs workflow-pack

| 冲突场景 | 默认优先者 | 裁决说明 |
|---------|-----------|---------|
| "给我备一节课" | lesson-planner | 单独备课请求 |
| "帮我做个教案" | lesson-planner | 单独教案请求 |
| "给我准备一整套课" | workflow-pack | 明确组合型请求 |
| "备课包" | workflow-pack | 明确组合型请求 |
| "完整备课" | workflow-pack | 明确组合型请求 |
| "教案 + PPT" | workflow-pack | 多模块组合 |
| "教案 + 练习" | workflow-pack | 多模块组合 |
| "备课 + PPT + 练习" | workflow-pack | 多模块组合 |

**裁决规则**：
- 单独备课/教案 → `lesson-planner`
- 完整备课/一整套课/多模块 → `workflow-pack`

---

### presentation vs workflow-pack

| 冲突场景 | 默认优先者 | 裁决说明 |
|---------|-----------|---------|
| "给我做个PPT" | presentation | 单独PPT请求 |
| "帮我做个课件" | presentation | 单独课件请求 |
| "备课 + PPT" | workflow-pack | 组合请求含PPT |
| "教案和PPT一起做" | workflow-pack | 组合请求 |
| "备课包" | workflow-pack | 组合请求 |

**裁决规则**：
- 纯PPT → `presentation`
- 组合请求含PPT → `workflow-pack`

---

### assignment vs tutor

| 冲突场景 | 默认优先者 | 裁决说明 |
|---------|-----------|---------|
| "出5道练习题" | assignment | 纯出题请求 |
| "布置一份作业" | assignment | 纯作业请求 |
| "帮我讲这道题" | tutor | 讲解请求 |
| "分析一下错因" | tutor | 辅导请求 |
| "讲解 + 出类似题" | workflow-pack | 组合请求 |
| "辅导包" | workflow-pack | 组合请求 |
| "不要直接给答案" | tutor | 启发式辅导特征 |

**裁决规则**：
- 出题/作业/练习 → `assignment`
- 讲解/错因/不要直接给答案 → `tutor`
- 讲解 + 练习 → `workflow-pack`（辅导包）

---

### admin vs workflow-pack

| 冲突场景 | 默认优先者 | 裁决说明 |
|---------|-----------|---------|
| "写个通知" | admin | 单独通知请求 |
| "帮我写评语" | admin | 单独评语请求 |
| "通知 + 反馈一起做" | workflow-pack | 组合请求 |
| "家校沟通包" | workflow-pack | 组合请求 |
| "准备一套家校沟通内容" | workflow-pack | 组合请求 |

**裁决规则**：
- 单独通知/评语/反馈 → `admin`
- 多模块家校沟通 → `workflow-pack`

---

### 控制类 vs 业务类

| 冲突场景 | 默认优先者 | 裁决说明 |
|---------|-----------|---------|
| "开启教师助手" | mode-switch | 控制类优先 |
| "关闭教师助手" | mode-switch | 控制类优先 |
| "运行评测" | evaluation | 控制类优先 |
| "运行回归测试" | evaluation | 控制类优先 |
| 任何业务请求 + 控制口令 | 控制类 | 控制类一律优先 |

**裁决规则**：
- 控制类 skill 一律优先于业务类 skill
- 不存在控制类与业务类同时路由的情况

---

### 后处理类 vs 主路由类

| 冲突场景 | 默认优先者 | 裁决说明 |
|---------|-----------|---------|
| 任何首轮请求 | 主路由类 | 后处理类不抢首轮 |
| 主任务完成后的整形 | quality-governor | 后置参与 |
| 任务完成后的日志记录 | observability | 后置参与 |

**裁决规则**：
- 后处理类 skill（quality-governor、observability）不得抢首轮主路由
- 只在主任务完成后作为后置步骤参与

---

## 三、冲突裁决流程

```
步骤1：检查是否为控制类请求
IF 匹配控制类触发词
   → 路由到控制类 skill
   → 结束（控制类优先，无需继续判断）

步骤2：检查是否为组合类请求
IF 匹配组合类触发词 OR 包含多模块
   → 路由到 workflow-pack
   → 结束

步骤3：检查单技能冲突
IF 同时匹配多个单技能触发词
   → 按冲突矩阵裁决
   → 选择优先者
   → 结束

步骤4：使用兜底策略
IF 无法裁决
   → 按关键词兜底规则路由
   → 结束
```

---

## 四、冲突场景示例

### 示例1：备课 + PPT

**用户请求**："帮我备课并做个PPT"

**分析**：
- 匹配 lesson-planner（备课）
- 匹配 presentation（PPT）
- 匹配 workflow-pack（组合请求）

**裁决**：`workflow-pack`（组合优先于单技能）

---

### 示例2：讲解 + 出题

**用户请求**："帮我讲这道题，再出几道类似的"

**分析**：
- 匹配 tutor（讲解）
- 匹配 assignment（出题）
- 匹配 workflow-pack（组合请求）

**裁决**：`workflow-pack`（辅导包）

---

### 示例3：通知 + 反馈

**用户请求**："帮我写个通知，再写一段学习反馈"

**分析**：
- 匹配 admin（通知、反馈）
- 匹配 workflow-pack（组合请求）

**裁决**：`workflow-pack`（家校沟通包）

---

### 示例4：单独教案

**用户请求**："给我备一节课"

**分析**：
- 匹配 lesson-planner（备课）
- 不匹配 workflow-pack（无组合）

**裁决**：`lesson-planner`

---

### 示例5：模式切换 + 业务请求

**用户请求**："开启教师助手，帮我备一节课"

**分析**：
- 匹配 mode-switch（开启教师助手）
- 匹配 lesson-planner（备课）

**裁决**：
1. 先执行 `mode-switch`（控制类优先）
2. 确认模式开启后，再执行 `lesson-planner`

---

## 五、冲突矩阵快速参考

| 冲突双方 | 典型冲突场景 | 默认优先者 |
|---------|------------|-----------|
| lesson-planner vs workflow-pack | 备课请求是否含组合 | 组合→workflow-pack，单独→lesson-planner |
| presentation vs workflow-pack | PPT请求是否含组合 | 组合→workflow-pack，单独→presentation |
| assignment vs tutor | 出题 vs 讲解 | 出题→assignment，讲解→tutor，组合→workflow-pack |
| admin vs workflow-pack | 行政请求是否含组合 | 组合→workflow-pack，单独→admin |
| 控制类 vs 业务类 | 模式/评测 vs 业务 | 控制类优先 |
| 后处理类 vs 主路由 | 整形/日志 vs 主任务 | 主路由优先，后处理只后置 |
