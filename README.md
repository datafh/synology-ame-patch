# synology-ame-patch

> 群晖 Synology AME（Advanced Media Extensions）编解码补丁脚本 —— **仅供测试与学习研究使用**

**作者：大鱼（数据渔港）** ｜ **官网：https://datafh.cn**

---

## ⚠️ 声明（必读）

> **本项目仅供测试与学习研究使用，请于 24 小时内自行删除，请支持正版软件。**
>
> - 本脚本会修改系统内的二进制文件，存在系统不稳定或数据风险
> - 使用本脚本产生的一切后果由使用者自行承担
> - 请勿用于任何商业用途
> - 群晖官方已将 AME 免费开放（仅需 Synology 账号在线激活），**正式使用请优先走官方渠道**

---

## 📖 简介

群晖部分机型在播放 HEVC（H.265）/ AAC 音视频时依赖 AME 套件的编解码授权。本仓库提供两个版本的补丁脚本，用于测试 AME 授权校验机制的学习研究：

| 脚本 | 适配环境 | 目标文件 MD5 |
|---|---|---|
| `ame71-2004.py` | DSM 7.1 + AME 3.x | `fcc1084f4eadcf5855e6e8494fb79e23` |
| `ame72-3005.py` | DSM 7.2 + AME 3.x | `09e3adeafe85b353c9427d93ef0185e9` |

两个脚本逻辑相同，仅补丁偏移量与校验值不同。脚本执行流程：

1. 校验目标二进制 `libsynoame-license.so` 的 MD5（不匹配即退出，防止误补）
2. 按偏移量写入补丁字节
3. 生成本地离线授权数据（HEVC + AAC）
4. 移除 CodecPack 的 AppArmor 配置并重命名其配置目录
5. 运行 `synoame-bin-check-license` 自检，通过后自动安装所需编解码器

---

## 🚀 使用方法

### 前提

- 群晖已开启 SSH（控制面板 → 终端机和 SNMP → 启用 SSH）
- AME 套件已安装（套件中心 → Advanced Media Extensions）
- 确认 `libsynoame-license.so` 的 MD5 与上表一致：

```bash
md5sum /var/packages/CodecPack/target/usr/lib/libsynoame-license.so
```

### 执行

SSH 登录群晖后，按 DSM 版本选择脚本：

```bash
# DSM 7.1
sudo python3 ame71-2004.py

# DSM 7.2
sudo python3 ame72-3005.py
```

看到 `Successful, updating codecs...` 和 `Done` 即完成，随后即可正常播放 HEVC/AAC 视频。

### 更新后失效怎么办

AME 或 DSM 升级会覆盖被补丁的文件，MD5 校验不通过时脚本会安全退出（`MD5 mismatch`）。此时需要等待适配新版本的脚本，或使用官方免费激活。

---

## 💡 官方免费激活方式（推荐）

2023 年起，群晖官方已取消 AME 的付费授权：

1. 套件中心安装 **Advanced Media Extensions**
2. 打开 AME → 使用 Synology 账号登录并激活（免费）
3. 激活后 HEVC/AAC 解码即可正常使用，无需任何补丁

> 仅当设备无法联网、账号激活异常等特殊场景下，本脚本才有测试研究价值。

---

## 📋 常见问题

**Q：脚本提示 `MD5 mismatch`？**
你的 AME 版本与脚本不适配（偏移量与校验值不匹配）。脚本此时不会改动任何文件，请勿手动强行修改。

**Q：补丁后 Video Station 还是放不了？**
AME 只负责解码授权；播放问题还可能与视频直解转码、字幕、容器格式有关。

**Q：会损坏数据吗？**
脚本只写 `CodecPack` 内的一个 .so 文件和授权 JSON，不触碰存储池与共享文件夹，但任何系统级修改都有风险，请自行评估并做好备份。

---

## 📄 许可与免责

本项目按「现状」提供，仅用于测试与学习研究，不提供任何明示或默示的担保。使用产生的任何后果由使用者自行承担。请支持正版，优先使用官方免费激活渠道。

Copyright © 大鱼（数据渔港）
