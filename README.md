# LoosqkTools

> 仓库：https://github.com/arxxyr/dsp-mods
> Thunderstore：`fablol/LoosqkTools`

戴森球计划（Dyson Sphere Program）的 mod 合集，作为**一个** Thunderstore 包发布，内含两个插件：

| 插件 | 功能 | 快捷键 |
|------|------|--------|
| **StellarAutoNavigation** | 星际自动导航：星图中按 `Ctrl` 选目标，按 `K` 或小键盘 `0` 启动自动飞行；穿过行星时自动脱离曲速刹停。 | `Ctrl`（设目标）、`K` / 小键盘 `0`（启动） |
| **AchievementsEnabler** | 装 mod 后也能解锁游戏内成就；同时拦截 Steam / RailWorks 成就上传与 Milky Way 数据回传。 | 无 |

辅助库 `Common`（日志、Utils）随插件一起分发。

## 构建

```bash
dotnet build LoosqkTools.sln -c Release
```

版本号定义在仓库根的 `Directory.Build.props`，所有 csproj 继承同一个 `<Version>`；`manifest.json` 与 `thunderstore.toml` 里的 `version_number` / `versionNumber` 需要手动同步修改。

## 鸣谢

本合集基于以下上游作者的工作，感谢并保留其署名：

- **Jimmie Clark** — `StellarAutoNavigation` 的原始实现，来源：[JClark/AutoStellarNavigation on Thunderstore](https://dsp.thunderstore.io/package/JClark/AutoStellarNavigation/)
- **Cyh** — `StellarAutoNavigation` 最早的参考实现：[Cyh/AutoStellarNavigation 1.0.4](https://dsp.thunderstore.io/package/Cyh/AutoStellarNavigation/1.0.4/)
- **code2X** — 跨星系导航修复：[code2X/DspMods@a83287f](https://github.com/code2X/DspMods/commit/a83287f40eb2a6cdcee6f4299e6d5966e4ee2c31)
- **PhantomGamers** — `AchievementsEnabler` 的原始实现，MIT 许可证，完整条款见 `AchievementsEnabler/LICENSE`，来源：[PhantomGamers/DSPAchievementsEnabler](https://github.com/PhantomGamers/DSPAchievementsEnabler)

本合集整合与维护 © 2026 loosqk。
