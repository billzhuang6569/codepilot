# 数据库初始化模块 (共享 CodePilot-Plan 模式)

## ⚠️ 重要说明
**在初始化阶段：**
- ✅ CodePilot 将使用一个名为 "codepilot-plan" 的共享 Supabase 项目来存储所有用户的任务管理数据。
- ✅ 如果 "codepilot-plan" 项目不存在，系统将引导您创建一个，并在其中初始化表结构（仅首次创建时）。
- ✅ 对于您的当前项目，系统将在 "codepilot-plan" 中创建一条新的项目记录，并初始化相关数据。
- ❌ 不进行任何用户项目代码的生产开发。
- ❌ 不创建用户项目的文件结构。
- ❌ 不编写用户项目的业务代码。

**所有实际开发工作将在初始化完成后，用户说"开始开发"时才进行。**

## 前置条件
确保 Supabase MCP 已配置成功，并且 Access Token 具有操作 "codepilot-plan" 项目（如果已存在）或创建新项目的权限。

## 初始化步骤

### 1. 准备 "codepilot-plan" Supabase 项目
你需要：
   a. 调用 `mcp_supabase_list_projects` 工具，查找是否存在一个名为 "codepilot-plan" 的 Supabase 项目。
   b. **如果 "codepilot-plan" 项目已存在**：
      i.   记录下它的项目ID (后续称 `SHARED_SUPABASE_PROJECT_ID`)。
      ii.  继续执行步骤 3。
   c. **如果 "codepilot-plan" 项目不存在**：
      i.   调用 `mcp_supabase_create_project` 工具创建一个新项目。参数设置：
          - 项目名称：`codepilot-plan`
          - 组织：询问用户选择哪个组织。
          - 区域：选择最近的区域。
      ii.  记录下新创建项目的ID (后续称 `SHARED_SUPABASE_PROJECT_ID`)。
      iii. **重要：由于是首次创建 "codepilot-plan"，需要在其中初始化表结构。请继续执行步骤 2。**

### 2. (仅当 "codepilot-plan" 首次创建时) 初始化 "codepilot-plan" 的表结构
在 `SHARED_SUPABASE_PROJECT_ID` 指向的 "codepilot-plan" 项目中，执行以下 SQL 命令创建表（如果表已存在则不会重复创建）。你需要使用 `mcp_supabase_apply_migration` 工具，可以将所有 `CREATE TABLE` 语句合并到一个迁移中，命名例如 `initial_codepilot_schema`。

```sql
-- 项目表 (存储每个用户通过CodePilot管理的项目)
CREATE TABLE IF NOT EXISTS projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(), -- 这个ID是用户在CodePilot中管理的具体项目的唯一标识
  user_defined_name VARCHAR(255) NOT NULL,    -- 用户为他们的项目取的名字
  description TEXT,
  status VARCHAR(50) DEFAULT 'planning',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 开发阶段表
CREATE TABLE IF NOT EXISTS development_phases (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE, -- 关联到 specific user project
  phase_number INTEGER NOT NULL,
  name VARCHAR(255) NOT NULL,
  description TEXT,
  status VARCHAR(50) DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT NOW()
);

-- 任务表
CREATE TABLE IF NOT EXISTS tasks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE, -- 关联到 specific user project
  phase_id UUID REFERENCES development_phases(id) ON DELETE CASCADE,
  task_code VARCHAR(50) NOT NULL, -- 项目内唯一的任务代码
  name VARCHAR(255) NOT NULL,
  objective TEXT,
  technical_notes TEXT,
  status VARCHAR(50) DEFAULT 'todo',
  priority INTEGER DEFAULT 5,
  estimated_hours DECIMAL(5,2),
  actual_hours DECIMAL(5,2),
  dependencies JSONB,
  created_at TIMESTAMP DEFAULT NOW(),
  completed_at TIMESTAMP,
  UNIQUE(project_id, task_code) -- 确保task_code在项目内唯一
);

-- 任务执行记录表
CREATE TABLE IF NOT EXISTS task_executions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  task_id UUID REFERENCES tasks(id) ON DELETE CASCADE,
  execution_date TIMESTAMP DEFAULT NOW(),
  ai_context TEXT,
  actions_taken JSONB,
  files_modified JSONB,
  conversation_thread_id VARCHAR(255),
  created_at TIMESTAMP DEFAULT NOW()
);

-- 项目上下文表
CREATE TABLE IF NOT EXISTS project_context (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE, -- 关联到 specific user project
  context_type VARCHAR(50) NOT NULL,
  content TEXT,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  UNIQUE(project_id, context_type) -- 确保每个项目同一种类型的上下文只有一条活跃记录
);

-- 代码架构映射表
CREATE TABLE IF NOT EXISTS code_architecture (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE, -- 关联到 specific user project
  module_name VARCHAR(255) NOT NULL,
  file_path VARCHAR(500),
  description TEXT,
  dependencies JSONB,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 创建性能优化索引
CREATE INDEX IF NOT EXISTS idx_development_phases_project_id ON development_phases(project_id);
CREATE INDEX IF NOT EXISTS idx_tasks_project_id ON tasks(project_id);
CREATE INDEX IF NOT EXISTS idx_tasks_status ON tasks(status);
CREATE INDEX IF NOT EXISTS idx_tasks_project_status ON tasks(project_id, status);
CREATE INDEX IF NOT EXISTS idx_task_executions_task_id ON task_executions(task_id);
CREATE INDEX IF NOT EXISTS idx_project_context_project_type ON project_context(project_id, context_type);
CREATE INDEX IF NOT EXISTS idx_code_architecture_project_id ON code_architecture(project_id);
```
**表结构创建完成后，继续执行步骤 3。**

### 3. 初始化当前用户项目的数据到 "codepilot-plan"
现在，我们需要为用户当前正在配置的这个新项目，在 "codepilot-plan" 的 `projects` 表中创建一条记录。
   a. **生成一个新的 UUID 作为当前用户项目的唯一标识** (后续称 `CURRENT_USER_PROJECT_UUID`)。这个 UUID 将用于在共享表中唯一标识此项目的数据。
   b. 获取用户为当前项目定义的名称（例如，在需求收集阶段获取的 `[项目名称]`）。
   c. 获取用户为当前项目定义的描述（例如，在需求收集阶段获取的 `[项目描述]`）。
   d. 使用 `mcp_supabase_execute_sql` 工具，在 `SHARED_SUPABASE_PROJECT_ID` 指向的 "codepilot-plan" 项目中执行以下 SQL：

#### 3.1 创建项目记录
```sql
-- 将 [CURRENT_USER_PROJECT_UUID] 替换为实际生成的UUID
-- 将 [项目名称] 替换为用户定义的项目名
-- 将 [项目描述] 替换为用户定义的项目描述
INSERT INTO projects (id, user_defined_name, description, status) 
VALUES ('[CURRENT_USER_PROJECT_UUID]', '[项目名称]', '[项目描述]', 'planning');
```

#### 3.2 创建开发阶段
```sql
-- 将 [CURRENT_USER_PROJECT_UUID] 替换为实际生成的UUID
INSERT INTO development_phases (project_id, phase_number, name, description) VALUES
('[CURRENT_USER_PROJECT_UUID]', 1, '项目初始化', '环境搭建和基础配置'),
('[CURRENT_USER_PROJECT_UUID]', 2, '核心功能开发', '实现主要业务功能'),
('[CURRENT_USER_PROJECT_UUID]', 3, '用户界面开发', '前端页面和交互'),
('[CURRENT_USER_PROJECT_UUID]', 4, '测试与优化', '功能测试和性能优化'),
('[CURRENT_USER_PROJECT_UUID]', 5, '部署上线', '项目部署和发布');
```

#### 3.3 (可选) 创建初始任务
根据架构设计（如果此时已有初步方案）创建具体任务，关联到 `[CURRENT_USER_PROJECT_UUID]`。这一步也可以在后续的开发流程中进行。

#### 3.4 初始化项目上下文
```sql
-- 将 [CURRENT_USER_PROJECT_UUID] 替换为实际生成的UUID
-- 将 [技术栈JSON] 替换为实际的技术栈信息（JSON格式）
-- 将 [PRD摘要] 替换为实际的PRD摘要
-- 将 [架构设计JSON] 替换为实际的架构设计信息（JSON格式）
INSERT INTO project_context (project_id, context_type, content) VALUES
('[CURRENT_USER_PROJECT_UUID]', 'current_focus', '项目初始化阶段'),
('[CURRENT_USER_PROJECT_UUID]', 'tech_stack', '[技术栈JSON]'),
('[CURRENT_USER_PROJECT_UUID]', 'prd_summary', '[PRD摘要]'),
('[CURRENT_USER_PROJECT_UUID]', 'architecture_design', '[架构设计JSON]');
```

#### 3.5 存储架构设计到 code_architecture 表
根据架构设计阶段确定的模块结构，将主要模块信息存储到 code_architecture 表：
```sql
-- 将 [CURRENT_USER_PROJECT_UUID] 替换为实际生成的UUID
-- 根据实际架构设计调整模块信息
INSERT INTO code_architecture (project_id, module_name, description) VALUES
('[CURRENT_USER_PROJECT_UUID]', '前端应用层', '用户界面和交互逻辑，使用[前端框架]实现'),
('[CURRENT_USER_PROJECT_UUID]', 'API服务层', '后端API接口，使用[后端框架]实现业务逻辑'),
('[CURRENT_USER_PROJECT_UUID]', '数据访问层', '数据库操作和数据模型定义'),
('[CURRENT_USER_PROJECT_UUID]', '用户认证模块', '用户注册、登录、权限管理'),
('[CURRENT_USER_PROJECT_UUID]', '核心业务模块', '根据PRD定义的主要功能模块'),
('[CURRENT_USER_PROJECT_UUID]', '通用工具模块', '公共函数、工具类、配置管理');
```

**注意**：上述模块信息应该根据架构设计阶段的具体输出进行调整。如果架构设计中定义了更具体的模块，请相应更新。

### 4. 验证当前用户项目初始化
使用 `mcp_supabase_execute_sql` 工具，在 `SHARED_SUPABASE_PROJECT_ID` 指向的 "codepilot-plan" 项目中执行以下 SQL，验证数据是否正确插入：
```sql
-- 将 [CURRENT_USER_PROJECT_UUID] 替换为实际生成的UUID
SELECT 
  p.user_defined_name as project_name,
  p.id as project_id,
  p.status as project_status,
  COUNT(DISTINCT dp.id) as phase_count,
  COUNT(DISTINCT t.id) as task_count,
  COUNT(DISTINCT pc.id) as context_count,
  COUNT(DISTINCT ca.id) as architecture_module_count
FROM projects p
LEFT JOIN development_phases dp ON p.id = dp.project_id
LEFT JOIN tasks t ON p.id = t.project_id
LEFT JOIN project_context pc ON p.id = pc.project_id
LEFT JOIN code_architecture ca ON p.id = ca.project_id
WHERE p.id = '[CURRENT_USER_PROJECT_UUID]'
GROUP BY p.id, p.user_defined_name, p.status;
```

同时验证项目上下文是否正确创建：
```sql
-- 将 [CURRENT_USER_PROJECT_UUID] 替换为实际生成的UUID
SELECT context_type, 
       CASE 
         WHEN LENGTH(content) > 50 THEN LEFT(content, 50) || '...' 
         ELSE content 
       END as content_preview
FROM project_context 
WHERE project_id = '[CURRENT_USER_PROJECT_UUID]'
ORDER BY context_type;
```

## 完成后
1. 向用户说明：
   - CodePilot 使用名为 "codepilot-plan" (ID: `SHARED_SUPABASE_PROJECT_ID`) 的共享 Supabase 项目来管理任务。
   - 您当前的项目 "[项目名称]" 已在 "codepilot-plan" 中成功注册，项目ID为 `CURRENT_USER_PROJECT_UUID`。
   - 相关的开发阶段和基础上下文已初始化。
2. **获得用户确认后**，再询问是否进入下一阶段。
3. **重要**：确保 `SHARED_SUPABASE_PROJECT_ID` 和 `CURRENT_USER_PROJECT_UUID` 这两个值能够传递到后续生成 `codepilot.mdc` 的步骤中，因为它们将分别对应模板中的 `{{SUPABASE_PROJECT_ID}}` 和 `{{PROJECT_UUID}}`。
