# CodePilot初始化指南

当用户输入类似"请确保 ./codepilot/codepilot_prompt.md存在，若存在，则按照文档内容，开始对话"的指令时，请按照以下步骤执行：

## 重要原则
- **面向非开发者**：使用通俗易懂的语言，避免技术术语
- **逐步确认**：每个阶段完成后，需要用户确认再继续
- **清晰引导**：明确告诉用户下一步要做什么

## 执行流程

### 阶段A：需求收集与PRD生成
1. 阅读并执行 `./codepilot/prompts/01_requirements_gathering.md` 中的所有步骤
2. 收集完需求后，**等待用户确认**信息无误
3. 基于收集的信息，阅读并执行 `./codepilot/prompts/02_prd_generation.md`
4. 生成PRD文档并保存到 `./codepilot/PRD.md`（注意：不是根目录）
5. **等待用户确认**PRD内容

### 阶段B：架构设计
1. PRD确认后，阅读并执行 `./codepilot/prompts/03_architecture_design.md`
2. 用通俗的语言解释架构设计
3. 展示架构设计并**等待用户确认**

### 阶段C：Supabase配置
1. 架构确认后，阅读并执行 `./codepilot/prompts/04_supabase_setup.md`
2. 帮助用户配置Supabase MCP
3. **重要提示**：配置完成后，提醒用户：
   - 左下角应该会弹出提示，请点击 **Enable**
   - 如果没有弹出，请检查Cursor设置中的MCP选项卡

### 阶段D：数据库初始化
1. MCP配置成功后，阅读并执行 `./codepilot/prompts/05_database_init.md`
2. 创建项目数据库和所有必需的表结构
3. 初始化项目数据
4. 将配置信息保存到 `./cursor/` 目录（与mcp.json同目录）

### 阶段E：Cursor Rules配置
1. 读取 `./codepilot/templates/cursorrules_template.md` 的**完整内容**
2. **严格按照模板内容**，仅替换以下占位符：
   - `{{SUPABASE_PROJECT_ID}}` → 实际的Supabase项目ID
   - `{{PROJECT_NAME}}` → 用户的项目名称
   - `{{PROJECT_UUID}}` → 创建的项目UUID
   - `{{TECH_STACK}}` → 架构设计中确定的技术栈（格式化为列表）
3. **重要**：保持模板的所有其他内容不变，包括：
   - 思想部分
   - Supabase MCP工具使用指南
   - 必须遵守的行为规范
   - 所有SQL查询示例
   - 项目特定配置
4. 将替换后的内容保存到 `./.cursor/codepilot.mdc`（注意文件名是codepilot.mdc，不是.cursorrules）
5. 如果无法创建带点的文件夹，提示用户手动创建：
   ```bash
   mkdir -p ./.cursor
   ```
6. 所有步骤成功后，提示用户：打开 ./.cursor/codepilot.mdc，将上方的Rule Type改为Always

### 阶段F：完成配置
1. 阅读并执行 `./codepilot/prompts/06_completion.md`
2. 向用户展示配置总结和使用说明
3. 用简单的语言说明如何开始使用:
   a. 提醒用户，可新建对话Thread，并说：`codepilot，开始编程`，即可开始工作任务
   b. 提醒用户，还可以使用`codepilot，审查`，即可随时让codepilot审查当前进展是否与数据库保持一致。经常审查，可以让进度始终同步。

## 文件保存位置
- **PRD文档**：`./codepilot/PRD.md`
- - **项目配置**：`./codepilot/`
- **MCP配置**：`~/.cursor/mcp.json`
- **Cursor规则**：`./.cursor/codepilot.mdc`（项目根目录下的.cursor文件夹）

## 重要提示
- 每个阶段都要等待用户确认后再继续
- 使用Supabase MCP工具时，明确告诉用户正在使用哪个工具
- 遇到错误时，清晰地向用户说明问题和解决方案
- 始终记住：用户可能没有编程经验，需要耐心引导 
