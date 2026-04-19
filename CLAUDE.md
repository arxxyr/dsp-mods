# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库概述

**LoosqkTools** 是作者自用的戴森球计划（Dyson Sphere Program）mod 聚合仓库，`LoosqkTools.sln` 下包含三个独立 BepInEx 5 插件：

| 子项目 | 用途 | 上游 |
|--------|------|------|
| `StellarAutoNavigation/` | 星际自动导航（Ctrl 在星图设目标，K / 小键盘 0 启动） | 修复参考 [code2X/DspMods](https://github.com/code2X/DspMods) |
| `AchievementsEnabler/` | 装 mod 后仍可解锁本地成就，并拦截 Steam/RAIL 上传与 Milky Way 数据回传 | 整合自 [PhantomGamers/DSPAchievementsEnabler](https://github.com/PhantomGamers/DSPAchievementsEnabler)（MIT，见 `AchievementsEnabler/LICENSE`） |
| `Common/` | 共享工具库（`Debug.cs` 日志、`Math.cs`、`Untils.cs`、`ModText.cs`），被其它插件 `ProjectReference` 引用 | — |

每个插件都是标准 BepInEx 5 插件（Harmony 补丁 + `BaseUnityPlugin`）。`AssemblyName` 在 csproj 内显式设定（注意 `StellarAutoNavigation/AutoStellarNavigate.csproj` 输出名是带 typo 的 **`AutoStellerNavigate.dll`**，作为上游已发布的插件名不要改）。

## 构建

### 本地

需要 **.NET SDK 8.0+**，不需要 DSP 游戏本体（引用程序集来自 NuGet 包 `DysonSphereProgram.GameLibs`，feed 是 `https://nuget.bepinex.dev/v3/index.json`）。

```bash
dotnet build LoosqkTools.sln -c Release
```

单项目：

```bash
dotnet build StellarAutoNavigation/AutoStellarNavigate.csproj -c Release
dotnet build AchievementsEnabler/AchievementsEnabler.csproj -c Release
```

### 本地部署到游戏

两个插件的 csproj 各自带 Windows 专用 `PostBuild` Target，通过 `$(APPDATA)` 引用 r2modman 的 `profiles\Default\BepInEx\plugins\loosqk-tools\` 目录，存在时自动 `xcopy` 部署；非 Windows 或该路径不存在会跳过。`loosqk-tools\` 是当前本地用的 r2modman 文件夹名；正式从 Thunderstore 安装后，r2modman 会创建 `fablol-LoosqkTools\`，届时同步修改两份 csproj 里的 `DspDeployDir`。

### CI

`.github/workflows/ci.yml`：push / PR 到 `main`/`master` 时在 `ubuntu-latest` 上用 .NET 8 SDK 构建整个 `LoosqkTools.sln`，产物上传到 GitHub Actions artifact（`LoosqkTools-dlls`）。

## 版本与依赖

仓库根 `Directory.Build.props` 是**整个合集的版本与身份信息唯一来源**（`<Version>` / `<Authors>` / `<Product>` / `<Copyright>`）。每次 bump 版本只改这一处；`BepInEx.PluginInfoProps` 会把 `<Version>` 注入到每个插件的 `PluginInfo.PLUGIN_VERSION`，`[BepInPlugin]` 特性读它。

`manifest.json` 和 `thunderstore.toml`（都在仓库根）里的 `version_number` / `versionNumber` 目前没有被 MSBuild 管理，需要手动同步改。这两个文件也只在仓库根存在一份，不是每个子插件一份。

三个 csproj 的其它依赖统一配置：

- `TargetFramework = net472`（DSP 在 Mono 上跑 .NET Framework 4.7.2；主流 DSP mod 如 CheatEnabler / UXAssist / AchievementsEnabler 都这么配）
- `UnityEngine.Modules 2022.3.62`（匹配 DSP 当前的 Unity 2022.3 LTS；关键点：此版本起 `UnityEngine.Input` 搬到了 `UnityEngine.InputLegacyModule`，与游戏运行时一致）
- `DysonSphereProgram.GameLibs 0.10.34.28470-r.0`（带 `IncludeAssets="compile"`，避免 NuGet 把 GameLibs 当运行时依赖传递出去）
- `BepInEx.Core 5.*` + `BepInEx.PluginInfoProps` + `BepInEx.Analyzers`

版本一致性要点：

- 旧版 Unity 2018.4 把 `UnityEngine.Input` 放在 `UnityEngine.CoreModule`，与 Unity 2022.3 运行时不匹配，会在游戏里抛 `TypeLoadException: Could not resolve type … UnityEngine.Input … UnityEngine.CoreModule`。必须用 ≥ 2019.4 的 UnityEngine.Modules 引用。
- GameLibs 最新版本到 `https://nuget.bepinex.dev/v3/package/dysonsphereprogram.gamelibs/index.json` 查。
- 三个 csproj 都带 `<RestoreAdditionalProjectSources>` 显式追加 `nuget.bepinex.dev` / `nuget.samboy.dev`（`UnityEngine.Modules 2022.3.62` 只在 BepInEx feed 上有）；没有根级 `Directory.Build.props`，改依赖要一个一个改。
- 条件 `Condition="'$(TargetFramework.TrimEnd(`0123456789`))' == 'net'"` 只有 `net472` 生效（会 TrimEnd 成 `net`），`netstandard2.0` 会 TrimEnd 成 `netstandard.` 不匹配。如果哪天要切回 netstandard，记得调这里。

## 子项目速查

### StellarAutoNavigation
- `AssemblyName = AutoStellerNavigate`（拼写 typo，已发布，保持不动）
- 核心逻辑：星图界面用 `Ctrl` 选目标，`K` / 小键盘 `0` 启动自动导航；穿过行星时自动脱离曲速
- 近期改动摘自 `git log`："updated the gamelibs, and fixed navigated based on https://github.com/code2X/DspMods/commit/a83287f40eb2a6cdcee6f4299e6d5966e4ee2c31"；"added auto stop feature"

### AchievementsEnabler
- `AssemblyName = AchievementsEnabler`
- 入口 `Plugin.cs`，集中在一个 `Patches` 类里用 Harmony 前缀把成就上传 / 异常检测类调用劫持掉
- 近期改动：适配 DSP 0.10.x（`AbnormalityLogic` 新通知入口、`SteamLeaderboardManager_*` 按面板拆分、`InitDeterminators` 清空注册）

### Common
- 无 `BepInPlugin`，只是工具库
- 目前只被 StellarAutoNavigation 引用；改动时记得同步 bump 依赖它的 mod

## 改动时的经验法则

- **AssemblyName / RootNamespace 拼写维持原样**（`AutoStellerNavigate`），这是已发布的 mod 身份，改名会 break 现有用户。
- **PostBuild 部署脚本**：如果要新增自动部署到游戏目录的步骤，模仿 `AutoStellarNavigate.csproj` 的写法，**一定加 `Condition="'$(OS)' == 'Windows_NT' And Exists(...)"`**，否则 CI 上会因 `xcopy` 不存在或 shell 转义失败报 MSB3073。用 `$(APPDATA)` 引用而不是写死用户名。
- **升级 GameLibs 版本**：改完一个项目的 `DysonSphereProgram.GameLibs` 版本后，其它项目要保持一致，避免符号签名不同导致 Harmony 补丁挂在错的目标类型上。
- **整合第三方 mod**：保留原作者的 `LICENSE` 文件在子目录里，并在 `README.md` 鸣谢部分列出上游链接。
