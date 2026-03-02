# 考拉通用教师助手 (Kaola Teacher Assistant)

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

一个基于 Agent 架构的智能教学辅助系统，帮助教师高效完成备课、课件设计、行政文案、学生辅导、作业练习等教学任务。

## ✨ 功能特性

### 核心功能

| 功能 | 描述 |
|-----|------|
| 📝 教案生成 | 6部分结构化教案，支持小学/初中/高中学段适配 |
| 📊 PPT成品内容包 | 逐页成品内容，包含标题、核心内容、展示建议、讲解备注 |
| 📋 作业/练习生成 | 结构化题目+答案，支持课堂练习、课后作业、分层练习 |
| 👨‍🏫 学生辅导 | 启发式讲解，错因分析，分步引导 |
| 📢 行政文案 | 通知、评语、家校沟通草稿 |
| 📦 工作流包 | 一键生成完整备课包、辅导包、家校沟通包 |

### 特色能力

- **学段智能适配**：自动识别小学/初中/高中，调整语言风格、内容密度、题目难度
- **个性化记忆**：支持教师偏好和班级画像，输出更贴合用户习惯
- **模式开关**：灵活控制项目上下文启用状态
- **模型路由**：根据任务类型自动选择合适的模型层级
- **质量治理**：统一输出风格，多模块整合去重

## 🏗️ 技术架构

### Agent 层级

```
Head Teacher（主控Agent）
    ├── lesson-planner（教案技能）
    ├── presentation（PPT成品内容技能）
    ├── admin（行政文案技能）
    ├── tutor（辅导讲解技能）
    ├── assignment（作业/练习技能）
    ├── workflow-pack（工作流包技能）
    ├── evaluation（评测治理技能）
    ├── mode-switch（模式控制技能）
    ├── observability（可观测性技能）
    ├── model-router（模型路由技能）
    ├── router-governor（路由治理技能）
    └── model-governor（模型治理技能）
```

### 工作流程

```
用户请求 → 学段识别 → 任务类型识别 → Skill路由 → 模型路由 → 子技能执行 → 结果验收 → 统一输出
```

### 记忆分层

| 层级 | 位置 | 用途 |
|-----|------|------|
| Layer 1 | 运行期状态 | 当前会话临时状态 |
| Layer 2 | `MEMORY.md` | 高频核心偏好 |
| Layer 3 | `profiles/` | 教师/班级/默认配置 |
| Layer 4 | `memory/` | 历史/经验/记录 |

## 📁 目录结构

```
teacher-assistant/
├── AGENTS.md              # 主控Agent规则
├── TOOLS.md               # 环境与工具说明
├── README.md              # 项目说明
├── MEMORY.md              # 常驻轻记忆
├── profiles/              # 结构化配置
│   ├── teacher-profile.md     # 教师偏好档案
│   ├── classroom-profile.md   # 班级画像
│   ├── defaults.md            # 系统默认配置
│   ├── mode-state.md          # 模式状态
│   └── model-routing.md       # 模型路由配置
├── memory/                # 长期档案
├── outputs/               # 输出目录
│   ├── lessons/           # 教案输出
│   ├── ppts/              # PPT大纲输出
│   ├── ppt_files/         # PPT成品内容包输出
│   ├── admin/             # 行政文案输出
│   ├── tutoring/          # 辅导讲解输出
│   └── assignments/       # 作业/练习输出
├── logs/                  # 日志目录
├── eval/                  # 评测目录
├── routing/               # 路由规则
├── models/                # 模型规则
├── templates/             # 模板目录
└── skills/                # 技能目录
```

## 🚀 快速开始

### 前置要求

本项目是一个基于规则的 Agent 系统，需要配合支持 Markdown 规则文件的 AI 助手使用。

### 安装步骤

1. **克隆仓库**

```bash
git clone https://github.com/your-username/teacher-assistant.git
cd teacher-assistant
```

2. **配置教师偏好（可选）**

编辑 `profiles/teacher-profile.md`，填写您的教学偏好：

```markdown
| 项目 | 内容 |
|-----|------|
| 常教学科 | 语文 |
| 常教年级 | 初二 |
| 常教学段 | 初中 |
```

3. **配置班级画像（可选）**

编辑 `profiles/classroom-profile.md`，填写班级整体信息。

### 使用示例

**小学教案**
> 给小学三年级上一节春天主题课。

**初中教案**
> 给初二语文《春》备一节40分钟的课。

**高中教学设计**
> 给高一语文做一节课的教学设计。

**分层作业**
> 根据这节课生成一份课后作业，分基础题和提高题。

**行政文案**
> 写一份下周家长会通知。

**完整备课包**
> 帮我准备一整套《春》的备课内容。

## 🔧 配置说明

### 教师偏好配置

在 `profiles/teacher-profile.md` 中可配置：

| 偏好类别 | 配置项 |
|---------|-------|
| 教案风格 | 简洁/详细/活动型/重点突破型 |
| PPT风格 | 简洁/重点突出/互动型 |
| 作业风格 | 题量偏好、分层偏好 |
| 辅导风格 | 启发式/分步细讲/简洁直接 |
| 沟通风格 | 正式/温和/简洁 |

### 模型路由配置

在 `profiles/model-routing.md` 中可配置任务类型与模型层级的映射：

| 任务类型 | 默认模型层级 | 优先级 |
|---------|-------------|-------|
| 教案生成 | Tier-1 | 质量优先 |
| PPT/课件 | Tier-1 | 质量优先 |
| 学生辅导 | Tier-1 | 质量优先 |
| 作业/练习 | Tier-2 | 平衡 |
| 行政文案 | Tier-3 | 成本优先 |

### 模式开关

通过口令控制项目上下文：

- **开启教师助手**：进入教师助手模式
- **关闭教师助手**：退出教师助手模式

## 🔌 集成到 OpenClaw 系统

### 方法一：作为独立项目使用

1. 将本项目放置在 OpenClaw 的 workspace 目录下
2. 在 AI 助手中加载 `AGENTS.md` 作为主控规则
3. 根据任务类型，AI 助手会自动读取对应的 `skills/` 目录下的技能文件

### 方法二：作为 OpenClaw 子模块

1. 在 OpenClaw 项目中添加本仓库为子模块：

```bash
git submodule add https://github.com/your-username/teacher-assistant.git workspace/teacher-assistant
```

2. 在 OpenClaw 的主配置中引用教师助手：

```markdown
# 在 OpenClaw 的主规则文件中添加
当用户请求与教学相关时，参考 `workspace/teacher-assistant/AGENTS.md` 进行处理。
```

### 方法三：自定义集成

1. 复制 `skills/` 目录下需要的技能文件
2. 复制 `templates/` 目录下需要的模板文件
3. 根据实际需求调整规则

## 📊 输出规范

### 教案输出格式

1. 课题信息
2. 教学目标
3. 教学重难点
4. 教学流程
5. 师生活动/板书建议
6. 作业设计

### PPT成品内容包格式

1. 封面页
2. 导入页
3. 核心知识讲解页（2-4页）
4. 示例/互动页
5. 课堂练习页
6. 总结页

### 作业/练习格式

1. 练习标题
2. 适用对象
3. 使用场景
4. 题目列表（基础/提高/拓展）
5. 参考答案
6. 使用建议

## 🧪 测试与评测

### 运行评测

评测用例位于 `eval/cases/` 目录，评测结果记录在 `eval/results/` 目录。

### 评测结果分类

| 结果 | 说明 |
|------|------|
| Pass | 核心行为符合预期，输出结构完整 |
| Partial Pass | 能完成任务，有轻微偏差 |
| Fail | 路由错误、结构缺失严重、核心功能不可用 |

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 创建 Pull Request

## 📝 更新日志

### v1.0.0

- 教案生成功能
- PPT成品内容包生成
- 作业/练习生成
- 行政文案生成
- 学生辅导功能
- 工作流包功能
- 学段适配能力
- 个性化记忆系统
- 模式开关功能
- 模型路由功能
- 多 Skill 路由稳定化
- 多模型路由稳定化

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

## 🙏 致谢

感谢所有为教育技术发展做出贡献的开发者和教育工作者。
