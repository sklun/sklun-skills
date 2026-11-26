# sklun-skills

个人 Codex Skills 仓库，集中维护可复用的操作流程、参考资料和模板。以本仓库为唯一源码，通过目录软链接供 Codex 加载。

## 技能列表

| 技能 | 用途 | 环境要求 |
| --- | --- | --- |
| [docker-remote](docker-remote/SKILL.md) | 识别当前主机，选择本地 Mini 或通过 SSH 访问的 OrbStack Fedora 环境，执行 Docker、Compose 和容器测试 | Mini 上的 OrbStack Fedora；其他主机可通过 SSH 访问 Mini；项目指定环境优先 |
| [feishu-cli](feishu-cli/SKILL.md) | 使用飞书 CLI 操作云文档、多维表格和知识库，支持从文档整理任务并创建任务表 | 已安装 `lark-cli`，具备相应登录态、权限和官方技能指南 |
| [siyuan-notes](siyuan-notes/SKILL.md) | 读取、整理和写入本地思源笔记，按已有分类保存并回读核对 | 已安装 `siyuan-cli`，思源已打开指定工作空间，认证环境变量可用 |
| [write-deployment-standard](write-deployment-standard/SKILL.md) | 编写或修订 Word、Markdown 部署标准和安装配置手册，核对配置、操作结果及排版 | 产品版本和相关资料；Word 交付时需要相应文档处理、渲染工具 |

具体连接方式、路径和操作约定以各技能的 `SKILL.md` 为准。

## 目录结构

```text
sklun-skills/
├── README.md
├── docker-remote/
│   └── SKILL.md
├── feishu-cli/
│   ├── SKILL.md
│   ├── agents/openai.yaml
│   └── references/task-base.md
├── siyuan-notes/
│   └── SKILL.md
└── write-deployment-standard/
    ├── SKILL.md
    └── agents/openai.yaml
```

每个技能使用独立目录，`SKILL.md` 包含名称、触发条件和操作步骤。按需添加 `references/`、`scripts/`、`assets/` 或 `agents/openai.yaml`。

## 配置软链接

本机仓库路径为 `/Users/sklun/Work/Code/Sklun/Git/sklun-skills`。Codex 加载入口默认位于 `~/.codex/skills`；设置了 `CODEX_HOME` 时，使用其下的 `skills` 目录。

在本仓库根目录执行以下命令。命令仅创建缺失入口；已有目录或软链接会跳过，并打印对应路径。

```shell
skills_repo="$(pwd -P)"
skills_dir="${CODEX_HOME:-$HOME/.codex}/skills"
mkdir -p "$skills_dir"

for skill_name in docker-remote feishu-cli siyuan-notes write-deployment-standard; do
  skill_target="$skills_dir/$skill_name"
  if [ -e "$skill_target" ] || [ -L "$skill_target" ]; then
    printf '%s\n' "已存在，请核对：$skill_target"
  else
    ln -s "$skills_repo/$skill_name" "$skill_target"
  fi
done
```

已有实体目录时，先比较内容并备份，再替换为软链接。已有软链接时，使用 `readlink` 核对目标；迁移仓库位置后，需要更新对应链接。

例如，以下命令应输出本仓库的 `docker-remote` 目录：

```shell
readlink "${CODEX_HOME:-$HOME/.codex}/skills/docker-remote"
```

软链接配置完成后，修改仓库文件即可更新加载入口读取的内容，无需复制文件。若 Codex 未显示技能更新，可重启后检查。

## 使用方式

在 Codex 中通过 `$技能名` 明确调用，也可根据任务描述自动选择技能。例如：

```text
使用 $docker-remote 检查当前项目的容器状态。
使用 $feishu-cli 将文档中的任务整理为飞书多维表格。
使用 $siyuan-notes 把这份排查结论保存到笔记。
使用 $write-deployment-standard 根据产品资料编写部署标准。
```

技能提供操作流程，实际操作范围按当前任务授权执行。

## 维护流程

1. 在仓库中创建或修改技能，保留其他任务已有的修改。
2. 修改 `SKILL.md` 时核对名称、描述、适用范围和操作步骤；新增技能或调整用途时同步更新本 README。
3. 按变更内容验证：检查 YAML、Markdown、相对链接和命令语法；修改脚本时验证其行为，修改触发条件时检查适用与不适用的请求。
4. 新增技能后，在 Codex 加载目录建立对应软链接，并核对目标和内容。
5. 检查 Git 差异，按当前任务约定提交和推送。用户要求暂不提交时，将变更保留在工作区。

常用检查命令，在仓库根目录执行：

```shell
git status --short
git diff --check
git diff
```

新增且尚未跟踪的文件需要直接打开核对，`git diff` 默认不会显示其正文。

软链接负责本机加载，Git 提交与推送负责版本保存和远程同步。系统技能和插件托管技能通过各自安装机制维护。
