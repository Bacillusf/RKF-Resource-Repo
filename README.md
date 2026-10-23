# RKF-Resource-Repo
RKF软件资源仓库
### 摘要
ReKernelFlasher的资源仓库，涵盖AnyKernel3(社区上传&批量编译)以及实用工具

## 目录结构

```
.github/workflows/                     # 批量编译工作流
├── 6.1_unified_build_.yml             # OORB-6.1  → AnyKernel3_Official/MIXKI/6.1.x/
├── unified_build.yml                  # OORB-6.6  → AnyKernel3_Official/MIXKI/6.6.x/
├── 6.12_unified_build_.yml            # OORB-6.12 → AnyKernel3_Official/MIXKI/6.12.x/
├── OORB-ONEP-BAKASU-unified.yml       # OORB-ONEP-BAKASU → AnyKernel3_Official/OORB/BakaSU/
├── OORB-ONEP-unified.yml              # OORB-ONEP        → AnyKernel3_Official/OORB/SukiSU/
├── Build Kernel OnePlus.yml           # 上游原版单机型手动编译 → AnyKernel3_Official/OORB/BakaSU/
└── Dependencies/                      # 各工作流所需的依赖文件（补丁/脚本/工具链缓存等）
    ├── 6.1-Dependency/                # ← cctv18/oppo_oplus_realme_sm8650
    ├── 6.6-Dependency/                # ← cctv18/oppo_oplus_realme_sm8750
    ├── 6.12-Dependency/               # ← cctv18/oppo_oplus_realme_sm8850
    ├── All-ONEP-Dependency/           # ← Numbersf/Action-Build（SukiSU-Ultra 分支）
    └── All-ONEP-BakaSU-Dependency/    # ← Numbersf/Action-Build（BakaSU 分支，原 ReSukiSU）

AnyKernel3_Official/
├── MIXKI/          # cctv18 系列（欧加真 OKI 通用内核）
│   ├── 6.1.x/{BakaSU,KSUN,SuKiSU,KSU}/
│   ├── 6.6.x/{BakaSU,KSUN,SuKiSU,KSU}/
│   └── 6.12.x/{BakaSU,KSUN,SuKiSU,KSU}/
└── OORB/           # 欧加真 OnePlus 全机型（Action-Build 系列）
    ├── BakaSU/
    └── SukiSU/

AnyKernel3_community/   # 社区上传
```

## 产物归档规则

编译工作流不再发布 Release，构建完成后由 `publish` job 将 `AK3_*.zip` 直接提交到本仓库对应目录（commit 带 `[skip ci]`）：

| 工作流 | 产品目录 | 说明 |
|---|---|---|
| `OORB-6.1` / `OORB-6.6` / `OORB-6.12` | `AnyKernel3_Official/MIXKI/<内核系列>.x/<管理器>/` | 文件夹由文件名中的管理器标识决定（BakaSU / KSUN / KSU / SuKiSU） |
| `OORB-ONEP-BAKASU` | `AnyKernel3_Official/OORB/BakaSU/` | 一加全机型 BakaSU |
| `OORB-ONEP` | `AnyKernel3_Official/OORB/SukiSU/` | 一加全机型 SukiSU |
| `Build Kernel OnePlus` | `AnyKernel3_Official/OORB/BakaSU/` | 单机型手动编译 |

示例：产物 `AK3_BAKASU_6.6.118_<KSUVER>_SUSFS_KPN_LZ4_DS_IN_SSG_BBG.zip`（来源 cctv18、内核 6.6.118、BakaSU）会归档到 `AnyKernel3_Official/MIXKI/6.6.x/BakaSU/`。

*尽请期待*
