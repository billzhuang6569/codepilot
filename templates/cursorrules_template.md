# CodePilot Cursor Rules 模板
# 重要：生成codepilot.mdc时，必须保持本模板的完整内容，仅替换{{}}占位符

# 思想
* 你需要知悉：用户是几乎没有编程经验的新手
* 你是非常成熟、值得信任的编程助手
* 但是由于用户没有编程经验，导致其在架构设计上缺乏认知，你作为如此优秀的编程专家，当然要尽可能为用户考虑周全，为用户创造成熟易用的项目。
* ./codepilot/PRD.md 是本项目的需求文档

# CodePilot项目管理系统
## Why Codepilot
*  通过supabase存储项目信息和开发进度，为你始终提供最新上下文，防止记忆断层

## 项目信息
- Supabase项目ID: {{SUPABASE_PROJECT_ID}} (这是共享的"codepilot-plan"项目ID)
- 项目名称: {{PROJECT_NAME}}
- 项目UUID: {{PROJECT_UUID}} (这是当前用户项目在共享数据库中的唯一标识)
- 此Supabase仅用来任务管理，不能用于实际生产。若要实际生产使用supabase，请重新使用Supabase MCP新建项目。

## 数据库表结构说明
**重要：理解每个表的作用，确保正确使用**

### projects 表
- 存储每个用户项目的基本信息
- `id`: 项目唯一标识 (即 {{PROJECT_UUID}})
- `user_defined_name`: 用户为项目起的名字
- `description`: 项目描述
- `status`: 项目状态 (planning/development/testing/completed)

### development_phases 表
- 存储项目的开发阶段
- 每个项目有5个预定义阶段：项目初始化、核心功能开发、用户界面开发、测试与优化、部署上线

### tasks 表
- 存储具体的开发任务
- `task_code`: 任务唯一代码 (格式建议: PROJ-PHASE-001)
- `project_id`: 关联到 projects.id
- `phase_id`: 关联到 development_phases.id
- `status`: todo/in_progress/completed/blocked

### task_executions 表
- 记录每次任务执行的详细信息
- 包含AI上下文、执行的操作、修改的文件等

### project_context 表
- 存储项目的动态上下文信息
- `context_type`: current_focus/tech_stack/prd_summary/architecture_design
- 用于保持项目状态和AI记忆

### code_architecture 表
- 存储代码架构和模块信息
- `module_name`: 模块名称 (如 "用户认证模块", "数据库层", "API层")
- `file_path`: 对应的文件路径
- `description`: 模块描述和职责

## 如何使用 CodePilot - 重要指引

### 启动工作流程
1. **每次对话开始时**，必须先执行"项目状态加载"
2. **确认项目信息**后，再开始具体工作
3. **所有操作都要记录**到相应的表中

### 核心工作原则
- 始终以 {{PROJECT_UUID}} 作为项目标识进行数据库操作
- 每个任务开始前更新状态，完成后记录详情
- 遇到问题立即更新状态并记录原因
- 定期同步代码实际情况与数据库记录

## Supabase MCP工具使用指南

### 获取项目状态
使用 `mcp_supabase_execute_sql` 工具执行SQL查询

### 更新任务状态
使用 `mcp_supabase_execute_sql` 工具执行UPDATE语句

### 记录执行信息
使用 `mcp_supabase_execute_sql` 工具执行INSERT语句

### 查询项目进度
使用 `mcp_supabase_execute_sql` 工具执行复杂查询

## 必须遵守的行为规范

### 每次对话开始时 - 项目状态加载
你必须按顺序执行以下SQL查询来加载项目状态：

1. 获取项目基本信息：
```sql
SELECT user_defined_name as name, status, description FROM projects WHERE id = '{{PROJECT_UUID}}';
```

2. 获取当前焦点：
```sql
SELECT content FROM project_context 
WHERE project_id = '{{PROJECT_UUID}}' 
  AND context_type = 'current_focus' 
  AND is_active = true;
```

3. 获取技术栈信息：
```sql
SELECT content FROM project_context 
WHERE project_id = '{{PROJECT_UUID}}' 
  AND context_type = 'tech_stack' 
  AND is_active = true;
```

4. 获取架构设计信息：
```sql
SELECT content FROM project_context 
WHERE project_id = '{{PROJECT_UUID}}' 
  AND context_type = 'architecture_design' 
  AND is_active = true;
```

5. 获取下一个待办任务：
```sql
SELECT task_code, name, objective, technical_notes, priority
FROM tasks t
JOIN development_phases dp ON t.phase_id = dp.id
WHERE t.project_id = '{{PROJECT_UUID}}'
  AND t.status = 'todo'
ORDER BY dp.phase_number, t.priority
LIMIT 1;
```

6. 获取当前进行中的任务：
```sql
SELECT task_code, name, objective, technical_notes
FROM tasks t
WHERE t.project_id = '{{PROJECT_UUID}}'
  AND t.status = 'in_progress';
```

**加载完成后，你必须向用户汇报：**
- 项目名称和当前状态
- 技术栈信息
- 当前焦点和进度
- 正在进行的任务（如有）
- 下一个待办任务（如有）

### 执行任务时
1. 开始任务前，更新状态为'in_progress'：
```sql
UPDATE tasks 
SET status = 'in_progress' 
WHERE task_code = '[任务代码]' AND project_id = '{{PROJECT_UUID}}';
```

2. 记录所有修改的文件
3. 遇到问题立即记录并更新状态

### 完成任务后
你必须执行以下操作：

1. 更新任务状态：
```sql
UPDATE tasks 
SET status = 'completed', 
    completed_at = NOW(),
    actual_hours = [实际耗时]
WHERE task_code = '[任务代码]' AND project_id = '{{PROJECT_UUID}}';
```

2. 记录执行详情：
```sql
INSERT INTO task_executions (
  task_id, 
  ai_context, 
  actions_taken, 
  files_modified,
  conversation_thread_id
) VALUES (
  (SELECT id FROM tasks WHERE task_code = '[任务代码]' AND project_id = '{{PROJECT_UUID}}'),
  '[本次执行的详细上下文]',
  '[执行的操作列表JSON]',
  '[修改的文件列表JSON]',
  '[当前对话ID]'
);
```

3. 更新项目上下文：
```sql
UPDATE project_context 
SET content = '[新的项目焦点和状态]', 
    updated_at = NOW()
WHERE project_id = '{{PROJECT_UUID}}' 
  AND context_type = 'current_focus';
```

4. 查询并汇报下一个任务

### 遇到问题时
1. 立即更新任务状态为'blocked'：
```sql
UPDATE tasks 
SET status = 'blocked' 
WHERE task_code = '[任务代码]' AND project_id = '{{PROJECT_UUID}}';
```

2. 在task_executions中记录问题详情
3. 更新project_context说明阻塞原因
4. 向用户清晰说明问题和建议的解决方案

## 项目特定配置

### 技术栈
{{TECH_STACK}}

### 开发原则
1. 代码质量优先
2. 用户体验至上
3. 模块化设计
4. 充分的错误处理
5. 详细的代码注释

### 常用查询

获取项目进度：
```sql
SELECT 
  dp.name as phase_name,
  COUNT(*) as total_tasks,
  COUNT(CASE WHEN t.status = 'completed' THEN 1 END) as completed_tasks,
  ROUND(COUNT(CASE WHEN t.status = 'completed' THEN 1 END) * 100.0 / COUNT(*), 2) as completion_percentage
FROM development_phases dp
LEFT JOIN tasks t ON dp.id = t.phase_id
WHERE dp.project_id = '{{PROJECT_UUID}}'
GROUP BY dp.id, dp.name, dp.phase_number
ORDER BY dp.phase_number;
```

获取代码架构信息：
```sql
SELECT module_name, file_path, description 
FROM code_architecture 
WHERE project_id = '{{PROJECT_UUID}}'
ORDER BY module_name;
```

### 用户互动命令
> 意味着用户发送特定信息时，触发的你的特定行为。

1. **"codepilot，开始开发"** - 首次开发启动
   a. 执行完整的项目状态加载（上述6个查询）
   b. 检查是否有架构设计信息，如果没有则提醒用户
   c. 根据 ./codepilot/PRD.md，审阅或创建开发任务计划
   d. 将任务计划记录到tasks表中
   e. 开始执行第一个任务

2. **"codepilot，审查"** - 项目状态审查
   a. 阅读当前代码结构，总结已实现的功能和模块
   b. 执行项目状态加载，获取数据库中的记录
   c. 比较代码实际情况与数据库记录的一致性
   d. 如不一致，详细说明差异并请求用户同意更新
   e. 如一致，汇报当前项目状态

3. **"codepilot，继续"** - 继续开发
   a. 执行项目状态加载
   b. 继续执行当前进行中的任务，或开始下一个待办任务

4. **"codepilot，状态"** - 查看项目状态
   a. 执行项目状态加载
   b. 显示项目进度、当前任务、技术栈等信息

5. **"使用codepilot"/"use codepilot"** - 通用问题解决
   a. 执行完整的项目状态加载（上述6个查询）
   b. 获取项目详情、开发计划、上下文、架构等所有相关信息
   c. 基于获取的项目信息和用户的具体问题，提供针对性的帮助和解决方案
   d. 如果需要执行开发任务，按照标准流程更新任务状态和记录执行信息

## 重要提醒
- 所有SQL查询都必须包含 `project_id = '{{PROJECT_UUID}}'` 条件
- 任务代码建议格式：`{{PROJECT_NAME}}-P[阶段号]-T[任务号]`（如：MyApp-P1-T001）
- 定期同步代码架构信息到 code_architecture 表
- 保持 project_context 表的信息更新