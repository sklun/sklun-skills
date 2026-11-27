---
name: feishu-cli
description: 使用本机飞书官方 lark-cli 定位和操作云文档、多维表格、原生任务清单及知识库。用户明确指定飞书 CLI，或需要从文档、表格整理任务、导入原生任务清单及迁入知识库时使用。
---

# 飞书 CLI

优先使用本机 `lark-cli`。用 `command -v lark-cli` 检查入口。macOS 下若已安装到 `/opt/homebrew/bin/lark-cli` 且当前 shell 的 `PATH` 缺少该目录，只为本次命令补充路径。

## 按操作选择官方指南

用 `lark-cli skills read <技能名>` 读取当前 CLI 随附的说明。需要命令细节时，按指南读取对应参考，并核对实际子命令的 `--help`。若 CLI 不提供 `skills read`，使用已安装的同名官方技能和命令帮助。

| 操作 | 官方指南 |
|---|---|
| 配置应用、检查登录、补充 OAuth 权限 | `lark-shared` |
| 读取或编辑 Docx 正文 | `lark-doc` |
| 云空间文件、文件夹、导入或导出 | `lark-drive` |
| 多维表格字段、记录、视图 | `lark-base` |
| 原生任务、任务清单、完成状态、自定义字段 | `lark-task` |
| 知识库空间、节点定位或移动 | `lark-wiki` |

`/wiki/` 链接标识知识库节点。若对象类型或位置不明，用 `wiki +node-get` 核实 `obj_type`、`obj_token`、`space_id` 和 `parent_node_token`。已知底层是 Docx 且只读正文时，可直接将知识库链接传给 `docs +fetch`。操作多维表格记录和视图时使用底层 Base token。

## 本机认证

- 个人资源的业务命令显式使用 `--as user`。遇到已知登录态的 macOS Keychain 访问错误，先检查当前执行环境是否能使用钥匙串，并按环境要求申请相应执行权限。保留钥匙串凭据存储。
- CLI 返回缺少权限时，按 `lark-shared` 针对报错的 scope 发起补充授权。设备授权使用 `--no-wait --json`，向用户展示官方验证链接和配套二维码。用户完成授权后，由 Codex 用该流程的设备码完成 CLI 登录，再继续原任务。设备码和凭据只用于完成当次认证。

## 常用工作流

- 用户需要原生任务清单、勾选完成、完成状态筛选，或将现有表格条目导入任务中心时，读取[原生任务清单与条目导入](references/task-native.md)。先区分原生 Task 与 Base 数据表，按用户选定的载体执行。
- 从云文档提取任务、生成静态预览、创建多维表格、迁入知识库或调整视图时，读取[任务多维表格](references/task-base.md)。
- 单模块操作直接使用相应官方指南。根据用户授权区分设计、读写和展示配置变更。写入后回读目标对象，核对任务涉及的关键字段、视图或目录位置。
