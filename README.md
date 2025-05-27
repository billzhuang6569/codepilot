# 🚀 CodePilot - AI驱动的智能项目管理系统

[![CodePilot](https://img.picui.cn/free/2025/05/25/6832b536ec93c.jpg)](https://woe.show)

<div align="center">
  
  [![快速开始](https://img.shields.io/badge/快速开始-2分钟部署-brightgreen?style=for-the-badge)](https://github.com/billzhuang6569/codepilot#快速开始仅需2分钟)
  [![查看文档](https://img.shields.io/badge/查看文档-使用指南-blue?style=for-the-badge)](https://github.com/billzhuang6569/codepilot#使用流程)
  [![提交反馈](https://img.shields.io/badge/提交反馈-Issues-orange?style=for-the-badge)](https://github.com/billzhuang6569/codepilot/issues)
  
</div>

## 什么是CodePilot？

CodePilot是一个专为零编程经验的创业者和创意者设计的智能项目管理系统。

它将你的想法转化为专业的产品需求文档（PRD），设计完整的系统架构，

并通过云端数据库持久化项目状态，让AI在不同对话中保持完整的项目记忆。


请记住，CodePilot的服务对象永远是：

**非开发者**
- 你没有任何编程经验，但是对Cursor等AI编程充满好奇
- 你有现实紧迫的需求，甚至尝试过使用AI编程，但体验极差

## 为什么需要CodePilot？

今天，AI让非开发者完全可以**编程**，但不一定可以**靠编程解决问题**。

### 主要痛点

- **概念障碍**：非开发者对基础概念都不了解（API？前端？后端？数据库？并发？），和AI对话十分费劲
- **调试困难**：非开发者阅读代码困难，出现BUG只能依赖模型自身能力
- **上下文丢失**：由于上下文限制，Cursor等工具可能无法做到前后行为一致，或对项目整体始终保持清醒理解
- **配置门槛**：即便可以配置supermemory等MCP，对普通人来说仍然有一定门槛

### 我的经历

如何让非开发者也能顺利Vibe Coding？作为0经验的开发者，我用Cursor编写了团队能够实际部署的：

- ✅ FastAPI集合，和飞书多维表格、消息等联动，实现自动化工作流
- ✅ 财务管理系统
- ✅ [WOE水印批量添加工具](https://wm.woe.show)
- ✅ 其他各种小尝试...

> **💡 关键发现**  
> 在这中间，我发现，**追踪项目进展，时刻Stick to the plan**，对编程成功率至关重要。
> 尤其是对代码几乎没有掌控力的我们 - 0经验Vibe Coder。

所以，我尝试使用自然语言做了CodePilot：

[![CodePilot](https://img.picui.cn/free/2025/05/26/6833bf087be70.png)](https://woe.show)


## 核心特性

### 🧠 永久记忆
- 使用Supabase云数据库保存所有项目状态
- AI永远知道项目进展到哪里
- 跨对话、跨设备的完整上下文保持

### 🏗️ 专业架构
- 自动生成企业级系统架构设计
- 包含前后端、数据库、安全、性能等完整方案
- 基于最佳实践的技术栈推荐

### 📋 智能管理
- 自动将需求分解为可执行的开发任务
- 实时追踪任务进度和完成状态
- 智能识别依赖关系和优先级

### 🎯 零门槛使用
- 无需任何编程知识
- 全程AI引导，自然语言交互
- 从想法到代码的一站式服务

## 快速开始（仅需2分钟）

### 准备工作

1. **创建项目文件夹** - 在您的电脑上新建一个空文件夹，用来存储您的项目
2. **打开Cursor** - 在Cursor中打开这个文件夹

### 一键启动

复制以下命令到Cursor中：

```
请执行以下操作：1. 使用git clone https://github.com/billzhuang6569/codepilot.git 下载CodePilot项目到当前目录 2. 确认 ./codepilot/codepilot_prompt.md 文件存在 3. 按照该文件的内容开始初始化CodePilot
```

就这么简单！AI会自动完成所有配置步骤。

**接下来，就和AI对话吧。**

> **💡 技巧**  
> 搞个语音输入法吧，效率翻倍！

## 使用流程

1. **需求收集** - AI会通过对话了解你的产品想法
2. **PRD生成** - 自动生成专业的产品需求文档
3. **架构设计** - 设计企业级的系统架构方案
4. **数据库配置** - 自动配置项目管理数据库
5. **开始开发** - AI按照任务列表逐步实现功能

## 如何使用 CodePilot

### 初始化完成后的日常使用

CodePilot 配置完成后，您可以通过以下命令与 AI 进行高效交互：

#### 🚀 开始开发
```
codepilot，开始开发
```
- 首次开发时使用
- AI 会加载项目信息、创建开发计划并开始执行

#### 🔍 查看项目状态
```
codepilot，状态
```
- 查看项目进度、当前任务、技术栈等信息
- 了解项目整体情况

#### ⚡ 继续开发
```
codepilot，继续
```
- 继续执行当前任务或开始下一个任务
- 适合中断后恢复开发

#### 🔎 项目审查
```
codepilot，审查
```
- 检查代码实际情况与计划的一致性
- AI 会对比并同步项目状态

#### 💡 通用问题解决
```
使用codepilot
```
或
```
use codepilot
```
- 遇到任何开发问题时使用
- AI 会加载完整项目信息后提供针对性帮助

### 💡 使用技巧

1. **语音输入**：建议使用语音输入法，效率更高
2. **自然对话**：直接描述您的需求，无需记忆复杂命令
3. **随时求助**：遇到问题直接说"使用codepilot"，AI会基于项目上下文提供帮助
4. **定期审查**：建议定期使用"codepilot，审查"确保项目进度同步

### 📁 项目文件说明

- **`./codepilot/PRD.md`** - 您的产品需求文档
- **`./.cursor/codepilot.mdc`** - Cursor AI 行为规则文件
- **共享数据库** - 所有任务和进度信息存储在 Supabase 的 "codepilot-plan" 项目中

## 项目结构

### 初始下载的CodePilot包含：

```
codepilot/
├── codepilot_prompt.md     # 核心初始化文件
├── prompts/                # 模块化的prompt文件
│   ├── 01_requirements_gathering.md
│   ├── 02_prd_generation.md
│   ├── 03_architecture_design.md
│   ├── 04_supabase_setup.md
│   ├── 05_database_init.md
│   └── 06_completion.md
├── templates/              # 模板文件
│   └── cursorrules_template.md
└── README.md              # 本文件
```

### 配置完成后会自动生成：

```
您的项目/
├── codepilot/          # CodePilot系统文件
│   └── PRD.md         # 您的产品需求文档
├── .cursor/           # Cursor配置文件目录
│   └── codepilot.mdc  # Cursor规则文件
└── [您的项目代码]     # AI生成的项目文件
```

## 常见问题

### Q: 需要什么前置条件？
**A:** 只需要安装Cursor编辑器，其他都由AI帮你完成。

### Q: 需要配置Supabase吗？
**A:** AI会一步步引导你完成，非常简单，全程不超过5分钟。

### Q: 可以用于商业项目吗？
**A:** 当然！CodePilot生成的是生产级别的代码和架构。

### Q: 如何继续之前的项目？
**A:** 新开对话后说"使用codepilot"或"codepilot，继续"，AI会自动加载所有状态。

### Q: 为什么要新建空文件夹？
**A:** 为了保持项目结构清晰，避免与其他文件混淆。

### Q: 忘记了命令怎么办？
**A:** 直接说"使用codepilot"或"help"，AI会根据项目状态提供帮助。无需记忆复杂命令。

### Q: 项目数据存储在哪里？
**A:** 所有项目管理数据存储在共享的"codepilot-plan" Supabase项目中，确保跨对话的数据持久性。

### Q: 可以同时管理多个项目吗？
**A:** 可以！每个项目都有独立的UUID，在共享数据库中完全隔离，互不干扰。

### Q: 如何查看项目进度？
**A:** 使用"codepilot，状态"命令，AI会显示详细的项目进度、当前任务和技术栈信息。

## 技术原理

CodePilot使用了以下技术实现智能项目管理：

- **Supabase MCP** - 实现数据持久化和状态管理
- **Cursor Rules** - 定制AI行为规范
- **结构化数据库** - 7张表完整记录项目全生命周期
- **智能任务分解** - 基于AI的任务规划和依赖分析

## 贡献指南

欢迎提交Issue和Pull Request！主要改进方向：

- 支持更多项目类型的模板
- 优化架构设计建议
- 增强任务分解算法
- 多语言支持

## 许可证

MIT License - 自由使用，包括商业项目

---

**立即开始，让AI成为你的编程合伙人！** 🎉

[GitHub](https://github.com/billzhuang6569/codepilot) | [问题反馈](https://github.com/billzhuang6569/codepilot/issues) 

## 下一步建议

1. **立即开始**：说"codepilot，开始开发"
2. **查看计划**：说"codepilot，状态"
3. **调整需求**：说"我想修改一下需求"
4. **遇到问题**：说"使用codepilot"

## 重要提示

- 所有代码和配置都会自动保存
- 不需要记住任何命令
- 用自然语言和AI对话即可
- AI会记住所有上下文，不用重复说明
- 支持多项目管理，每个项目独立追踪

准备好开始您的项目了吗？🚀
