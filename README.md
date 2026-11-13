# RKF-Resource-Repo
RKF软件资源仓库

### 摘要
ReKernelFlasher 的资源仓库，涵盖 AnyKernel3（社区上传 & 批量编译）以及实用工具。批量编译工作流**不再发布 Release / Tag**，构建完成后由 `publish` job 把 `AK3_*.zip` 直接提交到本仓库对应目录（commit 带 `[skip ci]`）。

## 目录结构

```
.github/workflows/                     # 批量编译工作流
├── 6.1_unified_build_.yml             # OORB-6.1        → AnyKernel3_Official/MIXKI/6.1.x/
├── unified_build.yml                  # OORB-6.6        → AnyKernel3_Official/MIXKI/6.6.x/
├── 6.12_unified_build_.yml            # OORB-6.12       → AnyKernel3_Official/MIXKI/6.12.x/
├── OORB-ONEP-BAKASU-unified.yml       # OORB-ONEP-BAKASU → AnyKernel3_Official/OORB/BakaSU/
├── OORB-ONEP-unified.yml              # OORB-ONEP        → AnyKernel3_Official/OORB/SukiSU/
├── Build Kernel OnePlus.yml           # 上游原版单机型手动编译 → AnyKernel3_Official/OORB/BakaSU/
├── cleanup.yml                        # 运行记录 / ccache / apt / thinlto 缓存清理
└── Dependencies/                      # 各工作流所需的依赖（补丁/脚本/ccache 等）
    ├── 6.1-Dependency/                # ← cctv18/oppo_oplus_realme_sm8650
    ├── 6.6-Dependency/                # ← cctv18/oppo_oplus_realme_sm8750
    ├── 6.12-Dependency/               # ← cctv18/oppo_oplus_realme_sm8850
    ├── All-ONEP-Dependency/           # ← Numbersf/Action-Build（SukiSU-Ultra 分支）
    └── All-ONEP-BakaSU-Dependency/    # ← Numbersf/Action-Build（BakaSU 分支）

AnyKernel3_Official/
├── MIXKI/          # cctv18 系列（欧加真 OKI 通用内核）
│   ├── 6.1.x/{BakaSU,KSUN,SuKiSU,KSU}/
│   ├── 6.6.x/{BakaSU,KSUN,SuKiSU,KSU}/
│   └── 6.12.x/{BakaSU,KSUN,SuKiSU,KSU}/
└── OORB/           # 欧加真 OnePlus 全机型（Action-Build 系列）
    ├── BakaSU/
    └── SukiSU/

AnyKernel3_community/   # 社区上传

CHANGELOG.md            # 版本变更记录
LICENSE                 # AGPL-3.0
```

## 产物归档规则

编译工作流不发布 Release，`publish` job 用 `actions/download-artifact` 收齐本次运行的 `AK3_*.zip`，按文件名中的管理器标识归档后 `git commit` + `git push`（同一分支，`[skip ci]`，并发组 `publish-ak3-official` 串行写入）：

| 工作流 | 产品目录 | 说明 |
|---|---|---|
| `OORB-6.1` / `OORB-6.6` / `OORB-6.12` | `AnyKernel3_Official/MIXKI/<内核系列>.x/<管理器>/` | 文件夹由文件名中的管理器标识决定（BakaSU / KSUN / KSU / SuKiSU） |
| `OORB-ONEP-BAKASU` | `AnyKernel3_Official/OORB/BakaSU/` | 一加全机型 BakaSU |
| `OORB-ONEP` | `AnyKernel3_Official/OORB/SukiSU/` | 一加全机型 SukiSU |
| `Build Kernel OnePlus` | `AnyKernel3_Official/OORB/BakaSU/` | 单机型手动编译 |

示例：产物 `AK3_BAKASU_6.6.118_<KSUVER>_NOSUSFS_KPN_LZ4_DS_IN_ADIOS_BBG.zip` 会归档到 `AnyKernel3_Official/MIXKI/6.6.x/BakaSU/`；`AK3_GKI_BAKASU_6.12.69_...` 与 `AK3_MTK_BAKASU_6.12.58_...` 同样按第二段（跳过 `GKI_` / `MTK_` 前缀）判定管理器。

## 依赖引用方式

- 依赖不再堆在仓库根目录，统一位于 `.github/workflows/Dependencies/<系列>/`，工作流内所有引用均改为该路径。
- OORB 系列：克隆上游 `Numbersf/Action-Build` 后，用 `cp -rf` 把 `Dependencies/All-ONEP-*.` 覆盖到克隆目录（overlay）；`Build Kernel OnePlus.yml` 同样使用 `All-ONEP-BakaSU-Dependency`。
- 磁盘扩容不再依赖第三方 action，直接 `curl ... | bash` 执行 `Dependencies/<系列>/scripts/maximize-build-space.sh`。
- 6.x 系列：单文件补丁走 `raw.githubusercontent.com` 拉取，`zram.zip`、ccache 同理；`builder_*.sh` 仅作本地手编参考，不参与 CI。

## CI 推送凭证

`publish` job 的 checkout 使用 `${{ secrets.RESOURCE_PAT || secrets.GITHUB_TOKEN }}`：

- 配置了 `RESOURCE_PAT`（fine-grained PAT：**Contents: Read and write**；若该 token 的推送会包含 `.github/workflows/**` 改动，另需 **Workflows: Read and write**）时优先用它；
- 未配置时自动回落到仓库自带 `GITHUB_TOKEN`（工作流内已声明 `permissions: contents: write`）。

## SUSFS 支持范围

仅 **BakaSU** 分支内核内置 SUSFS；`SukiSU Ultra` / `KernelSU-Next` / `KernelSU` 分支不再内置（`OORB-ONEP` 以 `SUSFS_META=-1` 关闭；`OORB-6.x` 在参数解析阶段置 `APPLY_SUSFS=n` 并写入 `CONFIG_KSU_SUSFS=n`）。产物名与 `ak3.log` 以 `SUSFS` / `NOSUSFS` 标识区分。

## 刷入须知

1. 刷入 AnyKernel3 前，***请务必备份 `boot.img`***，以便无法开机时救砖。
2. 只要内核版本相同即可刷入，无需在意编译时/Release 中提示的机型；高通机型选不带 `MTK` 标识的包，天玑机型选带 `MTK` 标识的包。
3. 命名中若出现具体机型（如 `ONEP13`），说明该包不可通用，请严格按机型刷入。
4. 请根据机型选择适合的刷机包，选错包导致的设备问题本仓库概不负责。

*尽请期待*
