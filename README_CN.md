# code2docs-ai-core

[English Version](README.md)

## GitHub Action: Code2Docs-ai Agent Workflow

![smartchat-mermaid-png-20250215073808](https://github.com/user-attachments/assets/900539d4-4ada-4105-9af9-8cc486f55da7)


本仓库包含一个 GitHub Action 工作流，允许您基于输入的存储库 URL 在当前组织中创建一个新存储库。

### 使用方法

要使用此 GitHub Action 工作流，请按照以下步骤操作：

1. 使用以下输入参数触发工作流：
   - `repoUrl`：GitHub 存储库的 HTTPS 格式 URL。
   - `branchName`：要使用的分支名称（默认值为 `main`）。

2. 工作流将执行以下步骤：
   - 在工作流日志中打印输入参数值。
   - 验证 `repoUrl` 是否为 GitHub.com 地址和 HTTPS 地址。如果不是，作业将失败。
   - 验证 `repoUrl` 是否存在于 GitHub 上并且是公开可访问的。如果不是，作业将失败。
   - 从 `repoUrl` 中提取 `orgName` 和 `repoName`，并将它们连接起来形成另一个参数 `docs_repo_name` = `orgName_repoName`。
   - 使用 GitHub API 检查存储库 `docs_repo_name` 是否存在。
   - 如果存储库存在，在创建新存储库之前将其删除。
   - 使用 GitHub API 在当前组织中创建一个名为 `docs_repo_name` 的新存储库。
   - 检查本地目录是否已存在，如果存在则将其删除，然后再克隆存储库。
   - 在工作流结束时，从 `repoUrl` 克隆源代码，使用 `branchName` 输入参数指定要克隆的分支。
   - 在运行任何步骤之前清理运行器上的工作目录。

### 示例

以下是触发工作流的示例：

```yaml
on:
  workflow_dispatch:
    inputs:
      repoUrl:
        description: 'GitHub 存储库的 HTTPS 格式 URL'
        required: true
      branchName:
        description: '要使用的分支名称'
        default: 'main'
```

这将触发工作流，并基于输入的 `repoUrl` 在当前组织中创建一个新存储库。

### 注意

工作流需要一个 `GITHUB_TOKEN` 密钥来进行身份验证并创建新存储库。

### 错误处理

`Create new repo` 步骤包括错误处理，以确保如果存储库创建失败，作业也会失败。用于创建存储库的 `curl` 命令包括 `--fail` 选项，以确保在错误时失败，并使用 `|| exit 1` 在存储库创建失败时退出作业。

此外，工作流现在包括验证，以检查存储库 `docs_repo_name` 是否存在，然后再尝试创建它。如果存储库存在，将在创建新存储库之前将其删除。这确保了存储库创建过程不会因具有相同名称的现有存储库而失败。

工作流还包括验证，以检查 `repoUrl` 是否存在于 GitHub 上并且是公开可访问的。如果 `repoUrl` 不存在或不可公开访问，作业将失败。这确保了存储库创建过程不会因无效或不可访问的 `repoUrl` 而失败。

### 额外步骤

工作流现在包括激活 Python 环境、运行 'aise-cli' 命令和使用 'aise-cli repo parse-repo' 解析存储库的额外步骤：

1. 激活 Python 环境：
   ```shell
   source ~/source/aise-cli/.venv/bin/activate
   ```

2. 运行 'aise-cli' 命令以测试 Python 环境是否正常工作：
   ```shell
   aise-cli
   ```

3. 删除源存储库并使用 'aise-cli repo parse-repo' 解析存储库：
   ```shell
   # 删除源存储库
   aise-cli repo delete-repo source_repo
   # 解析存储库
   aise-cli repo parse-repo --repo_path  aise-cli repo parse-repo --repo_name <source_repo absolute>
   ```

### 存储工作流运行状态

工作流现在包括在名为 `workflow_runs.json` 的 JSON 文件中存储工作流运行状态的功能。该文件包括每个工作流运行的以下值：
- 工作流运行 ID
- 输入参数中的 `repoUrl` 和 `branchName`
- `docs_repo_name`
- 当前状态和结论
- 工作流运行创建时间

`workflow_runs.json` 文件在每次工作流运行期间创建或更新，并将当前工作流运行的新记录添加到文件的现有内容顶部。然后将文件提交并推送到存储库。

### 更新工作流运行状态

工作流现在包括在末尾更新 `workflow_runs.json` 文件的步骤，状态设置为 'completed'，并使用当前时间更新 'completed_at' 字段。这确保了工作流运行状态被准确记录和更新。
