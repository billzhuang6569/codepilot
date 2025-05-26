# Supabase MCP配置指南 (共享 CodePilot-Plan 模式)

## ⚠️ 重要说明
**CodePilot 将使用一个名为 "codepilot-plan" 的共享 Supabase 项目来存储所有用户的任务和AI记忆。**
- 此配置步骤是为了确保您的 Cursor 编辑器能够通过 MCP 与 Supabase 服务通信。
- 您提供的 Supabase Access Token 需要有权限访问（如果已存在）或创建（如果不存在）名为 "codepilot-plan" 的 Supabase 项目。

**这个 "codepilot-plan" 数据库不是用于：**
- ❌ 您的应用程序生产数据库
- ❌ 存储您的应用程序的业务数据

如果您的应用程序本身也需要使用Supabase，请在项目开发阶段另外创建独立的Supabase项目。

## 目标
帮助用户配置 Supabase MCP，使AI助手能够直接操作 Supabase，特别是与 "codepilot-plan" 项目交互。

## 执行步骤

### 1. 检查MCP配置文件
```bash
# 检查是否已存在mcp.json
cat ~/.cursor/mcp.json 2>/dev/null || echo "MCP配置文件不存在"
```
如果输出 "MCP配置文件不存在"，或者内容不包含 Supabase MCP 配置，请继续。如果已存在且配置正确，可跳至步骤 5。

### 2. 创建或更新MCP配置
如果 `~/.cursor/mcp.json` 文件不存在，或没有 supabase mcp 配置，请指导用户创建或更新该文件，内容如下：
```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": [
        "-y",
        "@supabase/mcp@latest"
      ],
      "env": {
        "SUPABASE_ACCESS_TOKEN": "YOUR_ACCESS_TOKEN"
      }
    }
  }
}
```
如果文件中已有其他 MCP 配置，请确保将 "supabase" 部分正确添加或合并到 `mcpServers` 对象中。

### 3. 获取Supabase Access Token
指导用户获取一个 Supabase Access Token：
1. 访问 https://supabase.com/dashboard/account/tokens
2. 点击"Generate new token"
3. 输入token名称（例如：CodePilotSharedAccess）
4. 复制生成的token。**这个token未来将用于访问或创建 "codepilot-plan" 项目，以及所有CodePilot管理的项目数据。**

### 4. 更新配置文件
指导用户将获取的 `YOUR_ACCESS_TOKEN` 更新到 `~/.cursor/mcp.json` 文件中 `SUPABASE_ACCESS_TOKEN` 的值。

### 5. 启用MCP配置
**重要提示**：
- 更新 `~/.cursor/mcp.json` 后，Cursor界面的左下角通常会弹出提示，询问是否启用新配置的MCP。请点击 **Enable**。
- 如果没有弹出提示，请点击【Cursor】→【首选项】(Settings/Preferences) →【Cursor Settings】→【MCP】选项卡，找到 "supabase" MCP，并确保其开关已打开。

### 6. 验证MCP基础连接
使用 `mcp_supabase_list_organizations` 工具测试基础连接和Token是否有效。
如果成功列出组织，说明MCP配置和Access Token基本有效。后续步骤将进一步验证项目级别的操作。 