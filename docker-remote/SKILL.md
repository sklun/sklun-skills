---
name: docker-remote
description: 执行本机开发任务涉及的 Docker、Docker Compose、容器状态排查和容器测试时使用，识别本机是否为 Mini，选择本地或 SSH 访问的 OrbStack Fedora 环境，并遵循项目本地覆盖规则。
---

# Docker 环境选择与操作

## 选择环境

先读取当前项目生效的规则，检查是否指定远程主机及执行方式。项目已指定环境时使用该环境；未指定时采用以下默认值。

默认 Docker 环境是 Mini 上的 OrbStack Fedora。先识别当前主机，再选择本地执行或 SSH 访问。

### 识别本机

在 macOS 上读取主机标识：

```shell
hostname
scutil --get LocalHostName
scutil --get ComputerName
```

综合主机名、本地主机名、电脑名称和已知设备信息判断当前机器是否为 `mini.vpn.iKlun` 对应的 Mini。比较名称时忽略大小写及点号、连字符等分隔符；名称明确标识 Mini 时使用本地路径。无法确认是 Mini 时，使用 SSH 路径。仅有 Mac mini 硬件型号不足以确认是目标 Mini。

### 本机是 Mini

直接在本地通过 `of` 进入 OrbStack Fedora 环境执行，不再 SSH 到自身：

```shell
zsh -ic 'of docker ps'
```

仅在确认本地 `of` 不可用时，使用等价命令：

```shell
orb -m Fedora -u root docker ps
```

### 本机不是 Mini，或无法确认

通过 `ssh mini.vpn.iKlun`，在远端使用 `of` 前缀：

```shell
ssh mini.vpn.iKlun "zsh -ic 'of docker ps'"
```

仅在确认远端 `of` 不可用时，使用等价命令：

```shell
ssh mini.vpn.iKlun 'orb -m Fedora -u root docker ps'
```

运行其他 Docker 或 Compose 命令时，沿用选定的本地或远端前缀。项目规则若指定标准 Linux Docker 主机，则在指定远端直接运行 `docker` 或 `docker compose`，不套用上述 OrbStack 前缀；项目指定环境始终优先于 Mini 默认环境。

## 执行和核对

1. 按任务确定所选环境中的工作目录、Compose 文件、服务和操作范围，先用只读命令核对目标；不得因 Docker 连接失败而切换到其他未指定主机。
2. 从受限沙箱访问 SSH 或本地 OrbStack 若需要提权，直接使用 `sandbox_permissions: "require_escalated"`，并简要说明目标环境及操作用途。
3. 不在本机执行裸 `docker ...` 或 `docker compose ...`，除非用户明确要求使用本机 Docker。标准 Linux 远端执行原生命令不受此限制。
4. 仅执行任务已授权且必要的操作。遵循项目已有测试、构建和部署入口，以及服务范围和数据保护约定。
5. 测试默认在容器环境运行，Python 可使用本机已有虚拟环境。合适的容器环境不可用时报告限制，不回退到宿主机运行测试。未经用户明确要求，不为测试安装、升级、移除宿主机软件包或修改宿主机服务、配置。
6. 完成后根据任务核对命令退出状态、测试结果或目标服务状态，再报告实际结果。
