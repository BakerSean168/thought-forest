---
tags:
  - discipline
  - type/concept
  - status/growing
description: Android
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Resource]] | [[生活 MOC]]

---


# Andriod

## 文件系统

| **路径/名称**             | **作用**                                                                 | **权限/特性**                                                                 | **示例或说明**                                                                 |
|---------------------------|-------------------------------------------------------------------------|-----------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| **根目录 `/`**            | 文件系统的起点，包含所有子目录和挂载点                                   | 需 Root 权限访问敏感区域                                                    |                                                                               |
| `/system`                 | 存放系统核心文件（系统应用、库、配置文件等）                             | 默认只读，需 Root 才能修改                                                  | `/system/app`（预装应用）、`/system/lib`（库文件）、`/system/etc`（配置文件） |
| `/data`                   | 用户和应用数据存储的核心分区                                             | 普通应用仅限自身数据，Root 可全局访问                                        | `/data/app`（用户安装的 APK）、`/data/data`（应用私有数据）                   |
| `/cache`                  | 系统临时文件和 OTA 更新包                                                | 系统自动管理，Recovery 可清理                                               | OTA 更新包下载后暂存于此                                                      |
| `/proc`                   | 虚拟文件系统，提供内核和进程信息                                         | 动态生成，只读                                                              | `/proc/cpuinfo`（CPU 信息）、`/proc/meminfo`（内存使用）                      |
| `/dev`                    | 硬件设备接口文件（如块设备、字符设备）                                   | 系统级访问权限                                                              | `/dev/block`（存储设备分区信息）                                              |
| `/mnt` 或 `/storage`      | 挂载外部存储（内部模拟存储或物理 SD 卡）                                 | 用户可见的存储路径                                                          | `/storage/emulated/0`（内部存储）、`/storage/sdcard1`（物理 SD 卡）           |
| **内部存储**              | 用户可见的主存储空间（如照片、下载文件）                                 | 应用需权限访问公共目录                                                      | `/sdcard/DCIM`（相机照片）、`/sdcard/Android/data`（应用公共缓存）            |
| **外部存储**              | 物理 SD 卡或模拟存储分区                                                 | Android 10+ 受 Scoped Storage 限制                                          | `/storage/sdcard1`（物理 SD 卡路径）                                          |
| **应用私有存储**          | 应用专属数据（数据库、配置、缓存等）                                     | 仅应用自身可访问，卸载时删除                                                | `/data/data/<包名>/databases`（SQLite 数据库文件）                            |
| **公共存储**              | 用户媒体文件（音乐、文档等）                                             | 需 `READ/WRITE_EXTERNAL_STORAGE` 权限                                       | `/sdcard/Music`、`/sdcard/Documents`                                          |
| **特殊分区**              |                                                                         |                                                                             |                                                                               |
| `/boot`                   | 系统启动镜像（内核和初始化 RAM 磁盘）                                    | 只读，关键分区                                                              | `boot.img` 包含启动所需的核心文件                                             |
| `/recovery`               | Recovery 模式镜像                                                       | 用于系统恢复或刷机操作                                                      | 通过 `Volume Up + Power` 进入 Recovery                                        |
| `/vendor`                 | 厂商定制驱动和闭源库                                                     | 只读，厂商专属文件                                                          | 高通芯片驱动文件可能位于此                                                    |
| **关键文件**              |                                                                         |                                                                             |                                                                               |
| `/system/build.prop`      | 设备型号、Android 版本等系统属性                                         | 只读，Root 可修改                                                           | 修改后可能影响 OTA 更新                                                       |
| `/system/etc/hosts`       | 域名解析规则（可屏蔽广告或重定向）                                       | Root 可修改                                                                 | 自定义 DNS 或广告拦截                                                         |
| `/system/etc/fstab`       | 定义分区挂载规则                                                         | 系统启动时读取                                                              | 分区挂载点、文件系统类型（ext4、f2fs 等）                                     |
| **权限与安全**            |                                                                         |                                                                             |                                                                               |
| Linux 文件权限            | 通过 `rwx` 控制所有者、用户组和其他用户的访问                             | 例如 `drwxr-x--x` 表示目录所有者可读写执行，组用户可读执行，其他用户仅执行   | 使用 `chmod` 或 `chown` 修改权限                                              |
| SELinux                   | 强制访问控制（MAC），限制进程对资源的访问                                | 系统级安全策略，Root 后可能需调整策略                                       | 通过 `getenforce` 查看状态（Enforcing/Permissive）                             |
| **开发者工具**            |                                                                         |                                                                             |                                                                               |
| ADB Shell                 | 通过 `adb shell` 访问设备文件系统                                        | 需开启 USB 调试模式                                                         | `adb pull /sdcard/file.txt` 可导出文件                                        |
| Android Studio 文件浏览器 | 在 Android Studio 中查看设备文件（需 Root 权限访问 `/data/data`）        | 可视化操作文件                                                              | 路径：View > Tool Windows > Device File Explorer                              |

## 玩机软件

shizuku
scene

# drawin

苹果开源系统

