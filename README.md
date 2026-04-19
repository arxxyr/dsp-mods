# LoosqkTools

> GitHub: https://github.com/arxxyr/dsp-mods
> Thunderstore: `fablol/LoosqkTools`

A Dyson Sphere Program mod pack containing two plugins, shipped as a single Thunderstore package.
戴森球计划（Dyson Sphere Program）的 mod 合集，作为**一个** Thunderstore 包发布。

---

## English

### Plugins included

| Plugin | What it does | Shortcut |
|--------|--------------|----------|
| **StellarAutoNavigation** | Auto-pilot between star systems. In the starmap, press `Ctrl` on a target, then `K` (or numpad `0`) to launch auto-flight. The mech automatically drops out of warp when passing a planet. | `Ctrl` (set target), `K` / numpad `0` (start) |
| **AchievementsEnabler** | Lets modded saves keep earning in-game achievements. Blocks Steam / RailWorks achievement unlocks and Milky Way leaderboard uploads by default, each with an independent toggle. | — |

The shared utility library `Common` (logging, helpers) ships alongside the plugins.

### AchievementsEnabler config

Config file: `BepInEx/config/AchievementsEnabler.cfg` (auto-generated on first run).

| Option | Default | Description |
|--------|---------|-------------|
| `EnablePlatformAchievements` | `false` | If `true`, Steam / RAIL achievement unlocks are allowed. Keep `false` to prevent modded saves from popping platform achievements. |
| `EnableMilkyWayUpload` | **`true`** | If `true`, game records are uploaded to the Milky Way leaderboard. This is **decoupled** from local achievements — you can keep in-game achievements working while still contributing to Milky Way. Set to `false` to block uploads entirely. |

> **Heads-up**: Uploading modded gameplay to Milky Way may skew public statistics. If you're running balance-changing mods, consider setting `EnableMilkyWayUpload = false`. For pure quality-of-life mods (like StellarAutoNavigation in this pack), the impact is negligible.

### Dependencies

- **BepInEx 5.4.x** (Thunderstore: `xiaoye97-BepInEx-5.4.17`) — only hard runtime dependency.

### Build from source

Requires **.NET SDK 8.0+**. No DSP game install needed — the build pulls reference assemblies from `DysonSphereProgram.GameLibs` on the BepInEx NuGet feed.

```bash
dotnet build LoosqkTools.sln -c Release
```

The version number is defined in the root `Directory.Build.props`; all sub-projects inherit the same `<Version>`. Remember to sync `manifest.json` and `thunderstore.toml` when bumping.

### Credits

This pack integrates / repackages work by the following upstream authors. Huge thanks to all of them:

- **Jimmie Clark** — original `StellarAutoNavigation` implementation. Source: [JClark/AutoStellarNavigation on Thunderstore](https://dsp.thunderstore.io/package/JClark/AutoStellarNavigation/).
- **Cyh** — earliest reference implementation of StellarAutoNavigation: [Cyh/AutoStellarNavigation 1.0.4](https://dsp.thunderstore.io/package/Cyh/AutoStellarNavigation/1.0.4/).
- **code2X** — cross-system navigation fix: [code2X/DspMods@a83287f](https://github.com/code2X/DspMods/commit/a83287f40eb2a6cdcee6f4299e6d5966e4ee2c31).
- **PhantomGamers** — original `AchievementsEnabler` implementation, MIT licensed. Full license text at `AchievementsEnabler/LICENSE`. Source: [PhantomGamers/DSPAchievementsEnabler](https://github.com/PhantomGamers/DSPAchievementsEnabler).

Integration and maintenance of this pack © 2026 loosqk.

---

## 中文

### 包含的插件

| 插件 | 功能 | 快捷键 |
|------|------|--------|
| **StellarAutoNavigation** | 星际自动导航：星图中按 `Ctrl` 选目标，按 `K` 或小键盘 `0` 启动自动飞行；穿过行星时自动脱离曲速刹停。 | `Ctrl`（设目标）、`K` / 小键盘 `0`（启动） |
| **AchievementsEnabler** | 装 mod 后也能解锁游戏内成就。默认拦截 Steam / RailWorks 成就上传与 Milky Way 数据上传，二者都有独立开关。 | 无 |

辅助库 `Common`（日志、Utils）随插件一起分发。

### AchievementsEnabler 配置

配置文件：`BepInEx/config/AchievementsEnabler.cfg`（首次运行后自动生成）。

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `EnablePlatformAchievements` | `false` | 设 `true` 时允许 Steam / RAIL 解锁成就。保持 `false` 可以防止 mod 存档给你解锁平台成就。 |
| `EnableMilkyWayUpload` | **`true`** | 设 `true` 时允许把游戏数据上传到 Milky Way 排行榜。这一项与本地成就**解耦**——可以同时保留游戏内成就、又参与 Milky Way。设 `false` 则彻底阻止上传。 |

> **提示**：把装了 mod 的存档数据上传到 Milky Way 可能会污染公共统计。如果你跑的是改变平衡性的 mod，建议把 `EnableMilkyWayUpload` 设为 `false`。纯 QoL mod（比如本包的 StellarAutoNavigation）影响基本可忽略。

### 依赖

- **BepInEx 5.4.x**（Thunderstore：`xiaoye97-BepInEx-5.4.17`）—— 唯一硬性前置。

### 从源码构建

需要 **.NET SDK 8.0+**，不需要 DSP 游戏本体（引用程序集来自 BepInEx feed 的 `DysonSphereProgram.GameLibs`）：

```bash
dotnet build LoosqkTools.sln -c Release
```

版本号定义在仓库根的 `Directory.Build.props`，所有子项目继承同一个 `<Version>`；`manifest.json` 与 `thunderstore.toml` 里的 `version_number` / `versionNumber` 需要手动同步修改。

### 鸣谢

本合集整合 / 重打包了以下上游作者的工作，感谢并保留其署名：

- **Jimmie Clark** — `StellarAutoNavigation` 的原始实现：[JClark/AutoStellarNavigation on Thunderstore](https://dsp.thunderstore.io/package/JClark/AutoStellarNavigation/)
- **Cyh** — `StellarAutoNavigation` 最早的参考实现：[Cyh/AutoStellarNavigation 1.0.4](https://dsp.thunderstore.io/package/Cyh/AutoStellarNavigation/1.0.4/)
- **code2X** — 跨星系导航修复：[code2X/DspMods@a83287f](https://github.com/code2X/DspMods/commit/a83287f40eb2a6cdcee6f4299e6d5966e4ee2c31)
- **PhantomGamers** — `AchievementsEnabler` 的原始实现，MIT 许可证，完整条款见 `AchievementsEnabler/LICENSE`：[PhantomGamers/DSPAchievementsEnabler](https://github.com/PhantomGamers/DSPAchievementsEnabler)

本合集整合与维护 © 2026 loosqk。
