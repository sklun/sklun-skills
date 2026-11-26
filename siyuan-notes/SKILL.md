---
name: siyuan-notes
description: 使用 siyuan-cli 读取、整理或写入本机思源笔记。用户要求“记到笔记里”“记录到笔记”“保存为笔记”且未指定其他位置时使用，也用于明确的思源笔记操作。
---

# 思源笔记

## 本机连接

- 使用已安装的 `@frostime/siyuan-cli`，命令为 `siyuan-cli`。
- 连接名为 `Sklun`，区分大小写；`workspaceDir` 必须为 `/Users/sklun/Work/Note/SiYuan/Sklun`，由 CLI 自动发现思源当前端口。
- 操作前确保思源已打开该工作空间。使用 `zsh -ic` 加载 `.zshrc` 中的 `SIYUAN_TOKEN`，在任务子进程中清除 `SIYUAN_CLI_TOKEN` 覆盖值。
- Token 仅从环境变量读取，避免输出或写入笔记、命令参数及规则文件；不使用 `--reveal-token`。
- 每次业务命令显式指定 `--workspace Sklun`。

先核对连接和工作空间，并读取笔记本列表：

```shell
zsh -ic '
  set -e
  : "${SIYUAN_TOKEN:?SIYUAN_TOKEN 未加载}"
  unset SIYUAN_CLI_TOKEN
  export SIYUAN_CLI_WORKSPACE=Sklun
  siyuan-cli workspace show Sklun
  siyuan-cli workspace verify Sklun
  siyuan-cli api notebook.lsNotebooks --workspace Sklun --print json
'
```

只有 `siyuan-cli workspace list` 确认缺少 `Sklun` 连接时，才在已加载 Token 的 shell 中添加，再验证连接：

```shell
siyuan-cli workspace add Sklun \
  --workspace-dir /Users/sklun/Work/Note/SiYuan/Sklun \
  --token-env SIYUAN_TOKEN
```

若工作空间不匹配或不可连接，先解决连接问题，再进行笔记内容操作。

## 读取和写入

1. 需要读取或写入具体内容时，先查阅对应命令的 `--help`。可按需使用 `filetree.searchDocs`、`filetree.listDocsByPath`、`export.exportMdContent`、`filetree.createDocWithMd` 或块操作命令。
2. 按任务主题检查已有笔记分类和相关内容，选择现有笔记本与父文档。更新已有内容时先读取原文，保留用户已有修改。
3. 按用户要求创建、补充或整理笔记；用户明确指定其他工具、文件或位置时，按指定目标执行。
4. 对多行正文优先按命令帮助使用文件或标准输入，避免在 Shell 中拼接正文而引发转义或命令展开。
5. 写入后用返回的文档 ID 回读核对标题、正文、分类位置和链接；可使用 `export.exportMdContent` 和文档路径查询命令。尚未成功回读时，不报告核对完成。
6. 返回笔记标题及 `siyuan://blocks/<文档ID>` 链接。

所有业务命令继续使用前述认证环境，并显式添加 `--workspace Sklun`。
