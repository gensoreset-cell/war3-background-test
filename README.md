# Warcraft III 后台自动化测试

用于 Warcraft III 1.27、KKWE/JAPI 地图的隔离式自动化测试工具备份。工具在独立运行环境中启动游戏、按场景执行受控输入，并根据游戏产生的回执判断结果，同时保留测试证据，尽量不影响用户当前桌面的使用。

## 包含内容

下载仓库中的 [后台测试功能备份 ZIP](war3-background-test-backup-20261114-003750.zip)，其中保留原项目相对路径：

- `codexwork/scripts/war3-test/`：测试 CLI、场景配置解析和运行器。
- `codexwork/scripts/war3-65-cold/`：隔离启动、进程管理、输入及观察工具。
- `codexwork/config/war3-test/`：心跳、困难模式、路线和故障诊断等场景。
- `codexwork/docs/war3-test-ai.md`：操作手册，包含运行、结果判定和故障排查方法。
- 相关测试、研究记录及运行依赖指纹清单。

## 运行条件

需要 Windows 图形会话、Python 3.13、Warcraft III 1.27.0.52240，以及匹配的 KKWE/JAPI 运行依赖和测试地图。备份**不包含**游戏文件、KKWE/JAPI 二进制、地图或 Python 环境；`inputs.json` 只记录原环境文件的路径和 SHA-256 指纹。

将 ZIP 解压到项目根目录后，参照操作手册准备运行环境。场景中的地图和运行依赖路径基于原机器，迁移后需要按手册调整并核对地图指纹。

## 快速开始

在项目根目录打开 PowerShell，使用 Python 3.13：

```powershell
$war3Py = 'python'
$war3Cli = 'codexwork/scripts/war3-test/war3_test.py'

& $war3Py -B $war3Cli --help
& $war3Py -B $war3Cli env
& $war3Py -B $war3Cli list
& $war3Py -B $war3Cli validate --scenario native-hard-heartbeat
```

确认环境、地图指纹和场景配置后，使用全新的证据目录运行：

```powershell
$war3Evidence = 'codexwork/logs/war3-test/hard-smoke-' + (Get-Date -Format 'yyyyMMdd-HHmmss-fff')
& $war3Py -B $war3Cli run --scenario native-hard-heartbeat --evidence-dir $war3Evidence
Get-Content -Raw -Encoding UTF8 -LiteralPath "$war3Evidence/result.json"
```

每轮测试使用新的隔离目录。验收时检查 `result.json` 的终态、步骤和清理结果，并结合当轮游戏回执及截图判断；详细步骤见压缩包内的操作手册。

## 当前范围与限制

工具支持等待和断言游戏回执、输入、心跳检查、截图及等待退出。已有场景覆盖冷启动、困难模式、心跳、自然退出与故障诊断；其他地图需要准备对应的场景、坐标和可观察回执。

`native-h005-route` 的完整后台路线目前仍有第二次选人输入缺口，不能将其视为完整路线已通过。不同场景的成功结果仅证明各自实际断言的内容。
