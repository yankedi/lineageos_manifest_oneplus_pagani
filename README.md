# OnePlus 13T LineageOS 构建 Manifest

面向 OnePlus 13T（`PKX110` / `OP60F5L1` / `pagani`）的非官方 LineageOS 23.2 构建入口。

本仓库基于官方 [`LineageOS/android`](https://github.com/LineageOS/android) manifest，在完整 LineageOS / Android 16 源码树中加入 pagani 设备源码，并跟踪已经公开的 pagani Fork 和 Vendor 仓库。它不是 ROM 或刷机包，也不代表 pagani 已获得 LineageOS 官方支持。

## 当前状态

- 分支：`lineage-23.2`
- Android：16
- 设备：OnePlus 13T / `PKX110` / `pagani`
- 指纹完整录入、亮屏认证、支付宝指纹登录/支付和 Google Play 支付验证已在记录的 2026-08-29 构建上完成实机验证。
- AOD/熄屏指纹认证仍为 `NOT_TESTED`。
- 指纹修复内容已经通过完整 ROM、OTA 和真机验证；从全新空目录按本 manifest 完整同步并冷构建的流程尚未重新验证。

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

`repo sync` 会下载完整 LineageOS 平台源码、pagani 设备树、SM8750 内核、公共设备树和构建所需的 Vendor 文件，并自动使用本项目登记的 Fork。无需再逐个手动克隆或提取 proprietary blobs。

## Manifest 中的 pagani 项目

| 路径 | 仓库 | 跟踪分支 |
|---|---|---|
| `frameworks/base` | [`yankedi/oneplus-13T_android_frameworks_base`](https://github.com/yankedi/oneplus-13T_android_frameworks_base) | `lineage-23.2` |
| `device/oneplus/pagani` | [`yankedi/android_device_oneplus_pagani`](https://github.com/yankedi/android_device_oneplus_pagani) | `lineage-23.2` |
| `device/oneplus/sm8750-common` | [`yankedi/android_device_oneplus-13T_common`](https://github.com/yankedi/android_device_oneplus-13T_common) | `lineage-23.2` |
| `kernel/oneplus/sm8750-modules` | [`yankedi/android_kernel_oneplus-13T_sm8750-modules`](https://github.com/yankedi/android_kernel_oneplus-13T_sm8750-modules) | `lineage-23.2` |
| `kernel/oneplus/sm8750` | [`LineageOS/android_kernel_oneplus_sm8750`](https://github.com/LineageOS/android_kernel_oneplus_sm8750) | `lineage-23.2` |
| `kernel/oneplus/sm8750-devicetrees` | [`LineageOS/android_kernel_oneplus_sm8750-devicetrees`](https://github.com/LineageOS/android_kernel_oneplus_sm8750-devicetrees) | `lineage-23.2` |
| `hardware/oplus` | [`LineageOS/android_hardware_oplus`](https://github.com/LineageOS/android_hardware_oplus) | `lineage-23.2` |
| `vendor/oneplus/pagani` | [`yankedi/proprietary_vendor_oneplus_pagani`](https://github.com/yankedi/proprietary_vendor_oneplus_pagani) | `lineage-23.2` |
| `vendor/oneplus/sm8750-common` | [`TheMuppets/proprietary_vendor_oneplus_sm8750-common`](https://github.com/TheMuppets/proprietary_vendor_oneplus_sm8750-common) | `lineage-23.2` |

LineageOS 其余平台项目继续由官方 `lineage-23.2` manifest 管理。以上项目同样跟踪各自的 `lineage-23.2` 分支；执行 `repo sync` 会更新到当时的最新提交。

## Proprietary blobs

本 manifest 固定并同步经过当前构建使用的两棵 Vendor 树：

- pagani 专用文件来自 `PKX110_16.0.3.501(CN01)`；
- SM8750 common 文件来自设备树记录的 `CPH2653_16.0.9.401(EX01)` 基线。

因此正常执行 `repo sync` 后无需再运行 `extract-files.py`。这些仓库包含 OnePlus、高通及其他权利人的闭源文件；公开可访问不代表获得重新授权，使用者仍须遵守原始许可和适用法律。不要向这些仓库加入原厂 OTA、设备唯一信息、密钥或用户数据。

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
- `snippets/pagani.xml` 跟踪 pagani 相关源码和 Vendor 项目的 `lineage-23.2` 分支，因此不同时间同步得到的提交可能不同。
- 若要复现某次构建，应保存该次同步后生成的完整 `repo manifest -r`、构建环境、vendor 输入和产物哈希。
- 构建成功不等于可以安全刷写。刷写前仍需确认设备型号、槽位、AVB、分区容量、备份和回滚路径。

## 许可证与上游

官方 `LineageOS/android` manifest 当前没有仓库级 `LICENSE` 文件，本仓库不对上游 manifest 内容追加许可证声明。各个被引用的源码项目继续遵循各自仓库中的许可证。

提交上游 LineageOS 的修改请遵循其 Gerrit 和贡献规则。本仓库中的 pagani 设备适配与 Fork 不代表 LineageOS 官方认可或支持。
