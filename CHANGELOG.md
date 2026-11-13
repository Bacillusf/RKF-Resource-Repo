# 更新日志 (Change Log)

所有对本项目的重要更改都将记录在此文件中。
本日志格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
并且项目遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

## [Unreleased] (待发布)
### Added (新增)
- 待发布的功能更新

### Changed (变更)
- 待发布的功能变更描述

### Fixed (修复)
- 待发布的Bug修复描述

## [1.2.0] - 2026-11-13
### Added
- 新增 `publish` job：构建产物不再发布 Release / Tag，直接提交到 `AnyKernel3_Official/` 对应目录（commit 带 `[skip ci]`，并发组 `publish-ak3-official`）
- 归档目录骨架：`AnyKernel3_Official/MIXKI/<6.1.x|6.6.x|6.12.x>/<BakaSU|KSUN|SuKiSU|KSU>/`、`AnyKernel3_Official/OORB/<BakaSU|SukiSU>/`、`AnyKernel3_community/`

### Changed (变更)
- 目录结构对齐资源仓库规范：全部依赖迁入 `.github/workflows/Dependencies/`（`6.1/6.6/6.12-Dependency`、`All-ONEP-Dependency`、`All-ONEP-BakaSU-Dependency`），工作流内所有引用路径同步更新
- `All-ONEP-ReSuki-Dependency` → `All-ONEP-BakaSU-Dependency`；`OORB-ONEP-RESUKI-unified.yml` → `OORB-ONEP-BAKASU-unified.yml`（`name: OORB-ONEP-BAKASU`，AK3 标识 `AK3_RESUKI_` → `AK3_BAKASU_`）
- 磁盘扩容改为 `curl ... | bash` 执行仓库内 `maximize-build-space.sh`，移除 `.bootstrap` 稀疏检出与 `RUNNER_TEMP` 暂存两步
- `Build Kernel OnePlus.yml` 增加 `Package AnyKernel3` 打包与 `publish` 归档；apt 依赖补 `zip`
- 上传工件统一 `archive: false`，便于 `download-artifact` 直取 `AK3_*.zip`

### Removed (移除)
- 三个 6.x 工作流与两个 OORB 工作流的 `release` job（不再创建 tag / Release）

## [1.1.0] - 2026-11-13
### Added
- OORB-6.12 的 GKI 子版本由 `6.12.23` 换代到 `6.12.69`：新增 `*_69_gki` 入口（源码 `android16-6.12-2026-03`），移除 `*_23_gki` 入口
- OORB-ONEP 新增机型清单：`oneplus_n6x_b`、`oneplus_nord_ce6`
- 新增仓库级 `Cleanup` 工作流（清理运行记录与 ccache/apt/thinlto 缓存），配套 `scripts/cleanup-runs.sh`
- 依赖目录新增 `scripts/maximize-build-space.sh`（本地磁盘扩容），替代已停止维护的 `easimon/maximize-build-space`
- 依赖目录新增 `scripts/SusfsCompat.kts` 与 `scripts/SusfsContext.kts`，替代 `PatchFakePatches.kts`
- OORB 工作流新增 `Setup Kotlin`（Kotlin 2.4.10）与 `Fix libbpf for new glibc` 步骤

### Changed (变更)
- 上游更名：ReSukiSU → BakaSU，内核拉取地址改为 `Baka-SU/BakaSU`
- OORB-6.1 / 6.6 / 6.12 的 dispatch 输入全量更名 `resukisu_*` → `bakasu_*`，矩阵键、`ksu_type` 取值、KSU 标签同步改为 `bakasu`，AnyKernel3 文件名标识 `RESUKI` → `BAKASU`
- 依赖同步至上游 2026-11 版本：`local/builder_*.sh`、`FILE.md`、`check_rejects.sh`
- `Install Dependencies`：`liblz4-tool` → `lz4`，`kotlin` 改为独立安装
- 缓存键升级：apt-cache `v5`→`v6`，ccache/thinlto `16.8`→`17.0`
- `checkout` 升级至 `actions/checkout@v7`
- 6.1 / 6.6 / 6.12 统一工作流固定运行环境为 `ubuntu-24.04`
- OORB-ONEP 的 SukiSU 管理器 APK 工件名对齐上游（`Manager`）
- SUSFS 收窄到 BakaSU 分支：OORB-ONEP 固定 `SUSFS_META=-1`；OORB-6.1/6.6/6.12 新增「解析编译参数（susfs 仅 BakaSU）」步骤，非 BakaSU 置 `APPLY_SUSFS=n` 并跳过补丁/配置步骤，同时在 `CONFIG_KSU=y` 之后写入 `CONFIG_KSU_SUSFS=n`（对齐上游 `susfs_enable=false` 处理）
- 产物命名与 `ak3.log` 的 SUSFS 标识随开关输出（`SUSFS` / `NOSUSFS`）；OORB-ONEP 的 AK3 配置字段改为 `NOSUSFS_KPM_BBG_IN_FIX`

### Fixed (修复)
- 补丁鲁棒性：`50_add_susfs_in_gki-*`、`10_enable_susfs_for_ksu`、`002-zstd` 统一使用 `patch -p1 -F 3`
- `check_rejects.sh` 的 ERR trap 仅在确实存在 `.rej` 时退出，避免补丁步骤正常结束时误判
- DroidSpaces 配置补充 `CONFIG_MACVLAN=y`
- `6.12.69` 及以上子版本不再重复打入 `CVE_2026_43499` rtmutex 补丁

## [1.0.0] - 2026-8-1
### Added
- 新增欧加真6.1版本内核编译支持
- 新增欧加真6.12版本内核编译支持
