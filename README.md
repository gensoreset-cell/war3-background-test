# Warcraft III 后台测试工具

在 Windows 私有桌面上运行 Warcraft III 1.27 的地图测试。CLI 按场景启动游戏、发送输入、读取回执、保存截图，并清理该轮的进程和运行目录。支持单开，也支持并行运行两轮独立测试。

[下载通用移植包 ZIP](./war3-background-portable.zip)（约 2.82 MB）

压缩包包含 CLI、启动器、KKWE／JAPI 插件、运行清单和 AI 安装说明。需要在目标电脑准备 Python 3.13 x64、与清单匹配的 Warcraft III 1.27.0.52240 客户端，以及要测试的 `.w3x` 或 `.w3m` 地图。装配使用本机游戏和 MPQ 文件，测试在独立的运行目录内执行。

## 让 AI 安装

把 ZIP 交给目标电脑上的 Codex，并发送以下指令：

> 请按包内 `AGENTS.md` 安装并验证 War3 后台测试工具，然后接入本机 Codex 全局规则。

包根目录的 `AGENTS.md` 说明如何定位本机 Python 和 War3、装配运行目录、生成地图冒烟场景，以及配置 Codex 在本地实机测试时调用工具。

## 手动安装与预检

解压到一个新目录，在该目录打开 PowerShell。把客户端和地图示例路径改为你电脑上的真实路径。使用 uv 或独立安装的 Python 时，可以把命令中的 `py -3.13` 换成对应的 Python 3.13 x64 可执行文件。

```powershell
$war3Dir = 'D:\Games\Warcraft III'
$map = 'D:\Maps\待测地图.w3x'
$cli = 'codexwork/scripts/war3-test/war3_test.py'

py -3.13 -B -X utf8 codexwork/scripts/install_portable_war3.py --war3-dir $war3Dir --map $map
py -3.13 -B -X utf8 $cli env
py -3.13 -B -X utf8 $cli list
py -3.13 -B -X utf8 $cli validate --scenario map-window-smoke
```

## 实机测试

装配脚本会生成 `map-window-smoke` 场景。根据地图的加载提示调整场景中的操作和等待时间，然后运行：

```powershell
$evidence = 'codexwork/logs/war3-test/smoke-' + (Get-Date -Format 'yyyyMMdd-HHmmss-fff')
py -3.13 -B -X utf8 $cli run --scenario map-window-smoke --evidence-dir $evidence
```

双开时使用一次 `run-pair`，可以传入两个场景名或 JSON 路径。每个场景可配置自己的地图和 SHA-256。

```powershell
$evidence = 'codexwork/logs/war3-test/pair-' + (Get-Date -Format 'yyyyMMdd-HHmmss-fff')
py -3.13 -B -X utf8 $cli run-pair --scenario map-window-smoke map-window-smoke --evidence-dir $evidence
```

以输出目录中的 `result.json`、原始游戏回执、截图、退出和清理状态为准。双开的两轮日志分别在 `run-1` 和 `run-2`。自定义地图的选人、技能或任务验收需要配置对应操作和通过条件。
