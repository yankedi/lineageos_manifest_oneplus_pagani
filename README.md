# OnePlus 13T LineageOS 构建 Manifest

面向 OnePlus 13T（`PKX110` / `OP60F5L1` / `pagani`）的非官方 LineageOS 23.2 构建入口。

本仓库基于官方 [`LineageOS/android`](https://github.com/LineageOS/android) manifest，在完整 LineageOS / Android 16 源码树中加入 pagani 设备源码，并固定已经公开的指纹修复 Fork。它不是 ROM、刷机包或 proprietary blobs 仓库，也不代表 pagani 已获得 LineageOS 官方支持。

## 当前状态

- 分支：`lineage-23.2`
- Android：16
- 设备：OnePlus 13T / `PKX110` / `pagani`
- 指纹完整录入、亮屏认证、支付宝指纹登录/支付和 Google Play 支付验证已在记录的 2026-08-29 构建上完成实机验证。
- AOD/熄屏指纹认证仍为 `NOT_TESTED`。
- 四个 Fork 合并后的主线另外包含少量上游提交；当前 manifest 固定这些合并后 HEAD，但这一组合尚未重新完成整包构建和刷机验证。

完整修复说明、产物哈希和验证边界见 [`oneplus-13T-resources`](https://github.com/yankedi/oneplus-13T-resources/blob/main/docs/fingerprint-fix-2026-08-30.md)。

## 同步源码

先安装 Google `repo` 工具和 LineageOS 23.2 所需的构建依赖，然后执行：

```bash
mkdir -p ~/android/lineage
cd ~/android/lineage

repo init \
  -u https://github.com/yankedi/lineageos_manifest_oneplus_pagani.git \
  -b lineage-23.2 \
  --git-lfs \
  --no-clone-bundle

repo sync
```

`repo sync` 会下载完整 LineageOS 平台源码、pagani 设备树、SM8750 内核与公共设备树，并自动使用本项目登记的四个 Fork。无需再逐个手动克隆这些仓库。

## Manifest 中的 pagani 项目

| 路径 | 仓库 | 固定提交 |
|---|---|---|
| `frameworks/base` | [`yankedi/oneplus-13T_android_frameworks_base`](https://github.com/yankedi/oneplus-13T_android_frameworks_base) | `b788900f949f` |
| `device/oneplus/pagani` | [`yankedi/android_device_oneplus_pagani`](https://github.com/yankedi/android_device_oneplus_pagani) | `4d2cd9eb9ca7` |
| `device/oneplus/sm8750-common` | [`yankedi/android_device_oneplus-13T_common`](https://github.com/yankedi/android_device_oneplus-13T_common) | `62cf9629ebbb` |
| `kernel/oneplus/sm8750-modules` | [`yankedi/android_kernel_oneplus-13T_sm8750-modules`](https://github.com/yankedi/android_kernel_oneplus-13T_sm8750-modules) | `a9bb81bd2819` |
| `kernel/oneplus/sm8750` | [`LineageOS/android_kernel_oneplus_sm8750`](https://github.com/LineageOS/android_kernel_oneplus_sm8750) | `999b95d4792d` |
| `kernel/oneplus/sm8750-devicetrees` | [`LineageOS/android_kernel_oneplus_sm8750-devicetrees`](https://github.com/LineageOS/android_kernel_oneplus_sm8750-devicetrees) | `ebb25e3526ad` |
| `hardware/oplus` | [`LineageOS/android_hardware_oplus`](https://github.com/LineageOS/android_hardware_oplus) | `ec3b8211676f` |

LineageOS 其余平台项目继续由官方 `lineage-23.2` manifest 管理。设备相关项目固定到完整 commit SHA，以免同步时静默漂移。

## Proprietary blobs

公开源码不包含 OnePlus proprietary blobs。首次构建前必须从你有权使用的设备、原厂包或现有可安装包中提取：

```bash
cd ~/android/lineage
source build/envsetup.sh

./device/oneplus/pagani/extract-files.py
```

也可以把提取源路径作为参数交给 `extract-files.py`。该脚本会同时处理 pagani 与 `sm8750-common` 所需文件。不要把生成的 `vendor/oneplus`、原厂 OTA、分区镜像、密钥或设备唯一信息提交到本公开仓库。

如果 `breakfast pagani` 提示缺少 vendor makefile，先完成提取，再重新执行 `breakfast`。

## 构建

```bash
cd ~/android/lineage
source build/envsetup.sh
breakfast pagani
brunch pagani
```

也可以在完成 `breakfast pagani` 后执行：

```bash
mka bacon
```

产物位于：

```text
out/target/product/pagani/
```

NixOS 不是 Android 官方构建宿主环境，需要在能够提供标准 Linux FHS 和完整 Android 构建依赖的 `nix develop`、FHS 环境或容器内执行构建。本仓库不绑定任何本机绝对路径。

## 更新与复现边界

- 本 manifest 仓库自身跟随 LineageOS 官方 `lineage-23.2` manifest 历史。
- `snippets/pagani.xml` 固定 pagani 相关项目；需要升级时，应在完成同源整包构建和实机回归后再更新 SHA。
- 只固定设备相关项目并不能冻结全部 LineageOS 平台源码。若要逐字节复现某次发布，应另外保存该构建生成的完整 `repo manifest -r`、构建环境、vendor 输入和产物哈希。
- 构建成功不等于可以安全刷写。刷写前仍需确认设备型号、槽位、AVB、分区容量、备份和回滚路径。

## 许可证与上游

官方 `LineageOS/android` manifest 当前没有仓库级 `LICENSE` 文件，本仓库不对上游 manifest 内容追加许可证声明。各个被引用的源码项目继续遵循各自仓库中的许可证。

提交上游 LineageOS 的修改请遵循其 Gerrit 和贡献规则。本仓库中的 pagani 设备适配与 Fork 不代表 LineageOS 官方认可或支持。
