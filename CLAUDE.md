# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库概述

**DspMods** 是作者自用的戴森球计划（Dyson Sphere Program）mod 聚合仓库，一个 Visual Studio 解决方案下包含四个独立 BepInEx 插件：

| 子项目 | 用途 | 状态 |
|--------|------|------|
| `BetterStarmap/` | 星图界面功能扩展 | ✅ 可编译 |
| `StellarAutoNavigation/` | 星际自动导航（Ctrl 在星图设目标，K / 小键盘 0 启动） | ✅ 可编译 |
| `SelectBuilder/` | 框选拆除 / 升级 | ⚠️ **代码过期，依赖已被删除的 DSP API** |
| `Common/` | 共享工具库（`Debug.cs` 日志、`Math.cs`、`Untils.cs`、`ModText.cs`） | 被其它项目 `ProjectReference` 引用 |

每个插件都是标准 BepInEx 5 插件（Harmony 补丁 + `BaseUnityPlugin`）。`AssemblyName` 在 csproj 内显式设定（注意 `StellarAutoNavigation/AutoStellarNavigate.csproj` 输出名是带 typo 的 **`AutoStellerNavigate.dll`**，作为上游已发布的插件名不要改）。

## 构建

### 本地（推荐）

需要 **.NET SDK 8.0+**，不需要 DSP 游戏本体（引用程序集来自 NuGet 包 `DysonSphereProgram.GameLibs`，feed 是 `https://nuget.bepinex.dev/v3/index.json`）。

```bash
# 两个能编译的项目（Common 作为 ProjectReference 被自动拉取）
dotnet build BetterStarmap/BetterStarmap.csproj -c Release
dotnet build StellarAutoNavigation/AutoStellarNavigate.csproj -c Release
```

**整个 sln `dotnet build DspMods.sln -c Release` 目前编不过**，因为 `SelectBuilder/SelectedBuild.cs` 使用了新版 DSP 已删除的符号（`GIRer`、`PlayerAction_Build.previewPose / ClearBuildPreviews / AddBuildPreview / cursorWarning / castObjId` 等）。

### 本地部署到游戏

`StellarAutoNavigation/AutoStellarNavigate.csproj` 里有一段 Windows 专用的 `PostBuild` Target，会在存在 `C:\Users\Jimmie\AppData\Roaming\r2modmanPlus-local\DysonSphereProgram\profiles\DSP_Default_newgame\BepInEx\plugins\JClark-AutoStellarNavigation\` 时自动 `xcopy` 部署；非 Windows 或该路径不存在会跳过。要在自己机器上启用，改路径或复制这个 Target 到其它项目即可。

### CI

`.github/workflows/ci.yml`：push / PR 到 `main`/`master` 时在 `ubuntu-latest` 上用 .NET 8 SDK 构建 `BetterStarmap` + `StellarAutoNavigation`，产物上传到 GitHub Actions artifact（`DspMods-dlls`）。**SelectBuilder 目前被排除在 CI 外**；修好代码后再把它加回 `ci.yml` 的 build 步骤里。

## 版本与依赖

所有项目共用：
- `TargetFramework = netstandard2.0`
- `BepInEx.Core 5.*` + `BepInEx.PluginInfoProps` + `BepInEx.Analyzers`
- `UnityEngine.Modules 2018.4.12`（Unity 引用程序集；DSP 已升到 Unity 2022.3 LTS，但旧引用程序集仍 ABI 兼容）
- `DysonSphereProgram.GameLibs 0.10.28.*`（Phantom Gamers / Windows10CE 维护的 DSP 游戏程序集，**不是** 最新 0.10.33；如需最新 API 请手动升包版本）

各子项目都用 `<RestoreAdditionalProjectSources>` 显式追加 `nuget.bepinex.dev` / `nuget.samboy.dev`，没有根级 `Directory.Build.props`，改依赖要一个一个改。

## 子项目速查

### BetterStarmap
- 入口文件在 `BetterStarmap/` 根目录下的 `Plugin.cs`（或类似命名，BepInEx `[BepInPlugin]`）
- 依赖 `Common` 里的工具

### StellarAutoNavigation
- `AssemblyName = AutoStellerNavigate`（拼写有 typo，已发布，保持不动）
- 核心逻辑：在星图界面用 `Ctrl` 选目标，快捷键触发自动飞船规划
- 近期改动摘自 `git log`："updated the gamelibs, and fixed navigated based on https://github.com/code2X/DspMods/commit/a83287f40eb2a6cdcee6f4299e6d5966e4ee2c31"；"added auto stop feature"

### SelectBuilder（**待修**）
- `SelectedBuild.cs` 里用到的如下符号在最新 DSP `Assembly-CSharp.dll` 中已被删除或改名，需要按新 API 重写：

| 旧 API | 新版情况 |
|--------|---------|
| `GIRer.DrawFrame2D` / `DrawRect2D` | 整个 `GIRer` 类已删除 —— 需要换一套绘制手段（`GL` / `UnityEngine.UI` / `Handles`）|
| `PlayerAction_Build.previewPose` | 已删除 |
| `PlayerAction_Build.ClearBuildPreviews` | 已删除 |
| `PlayerAction_Build.AddBuildPreview` | 改名为 `AddBuildPreviewModel`，签名可能也变 |
| `PlayerAction_Build.cursorWarning` | 已删除 |
| `PlayerAction_Build.castObjId` | 已删除 |
| `PlayerAction_Build.upgradeLevel` / `cursorText` / `buildPreviews` | 仍存在 |

### Common
- 无 `BepInPlugin`，只是工具库
- 改动时记得同步 bump 依赖它的 mod

## 改动时的经验法则

- **不要依赖 sln 编译**：总是指定具体 `.csproj`，否则 SelectBuilder 会拖死整个构建。
- **AssemblyName / RootNamespace 拼写维持原样**（`AutoStellerNavigate`），这是已发布的 mod 身份，改名会 break 现有用户。
- **PostBuild 部署脚本**：如果要新增自动部署到游戏目录的步骤，模仿 `AutoStellarNavigate.csproj` 的写法，**一定加 `Condition="'$(OS)' == 'Windows_NT' And Exists(...)"`**，否则 CI 上会因 `xcopy` 不存在或 shell 转义失败报 MSB3073。
- **升级 GameLibs 版本**：改完一个项目的 `DysonSphereProgram.GameLibs` 版本后，其它项目要保持一致，避免符号签名不同导致 Harmony 补丁挂在错的目标类型上。
