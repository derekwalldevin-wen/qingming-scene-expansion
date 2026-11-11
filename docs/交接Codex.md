# 交接给 Codex：清明上河图三维场景扩充（投稿前收尾）

> 交接时间：2026-11-11 17:40（Asia/Shanghai）
> 交接人：DSH 会话（Lead + building-integrator 子代理）
> 工作目录：`C:\Users\derek\Documents\ChatGPT\清明上河图`
> 目标仓库（已推送）：<https://github.com/derekwalldevin-wen/qingming-scene-expansion>

---

## 0. 三十秒摘要

以《清明上河图》为依据，在**不改动冻结版 r1、不修改 dist** 的前提下，用 Aholo/Lux3D 付费生成 + Blender 处理 + overlay 运行时注入，做了一套**独立的建筑扩充资产包与可进入的三维场景预览**：

- 16 个原画锚点建筑替换（13 个候选模型，多数升级到 v2/v3/v4）
- 8 件地标/补充建筑（塔、寺、樊楼、州桥、水门、官署、仓房、酒肆）
- 6 件第二梯队市井建筑（民居甲乙、茶坊、学塾、香药铺、脚店）
- 程序化补齐：城墙（夯土收分、垛口、马面、敌楼、战棚、护城河）、河岸木栏、石板街、巷道、场院、民居、人物 12 个
- 资产包 **98 件 / 649,094,397 B**，CRC 通过
- 付费台账：**85 个任务、结算 1566、余额 3**
- 冻结 r1 与 dist 三文件 SHA 全程未变（下方有实测值）

---

## 1. 关键事实（已实测，可直接引用）

| 项 | 值 |
|---|---|
| 冻结 r1 源包 | `output/final-20261108/source/menghui-bianjing-20261108-r1-source.zip`<br>849,166,430 B / SHA-256 `B88E652A52A792F5CC528BF25006FFA3BCA2398992512238F62B5FAF69E71D75` / mtime 2026-11-08 23:09 |
| `dist/src/main.js` | `73CD7093173728891903DF409DE5B672B2B926DC08905284FA73D4ECDB03FAC7` |
| `dist/src/world.js` | `C3F06A7DFF5F811D9C09876134E296092FDB469541AE77A2A8681DF597DE6298` |
| `dist/index.html` | `2C56439FB6C7CACFFDB4D42728EC796CC00D861FE9F71490CC33F2BD21178854` |
| 资产包 | `output/building-expansion-20261109-v1/package/buildings-asset-pack.zip`<br>649,094,397 B / SHA-256 `012df3e706de2e657cc5c9e070ccded18d197f0a0bf82a5fa0d241cc0363ebf2` / 98 模型 / CRC 通过 |
| 资产清单 | `package/manifest.json`（98 件）、`package/SHA256SUMS.txt` |
| 付费台账 | `provider/ledger.json`（85 项）、`provider/private/account.json`（余额 3） |
| 授权 | `budget-authorization.json`（上限 1586，含 20 未扣费失败的保守预留说明） |
| 对外快照 | `PUBLISH/` 本地 git `master`，最新 `66c3152`，工作区干净，已 push 到上面的 GitHub |

---

## 2. 目录地图（只列会用的）

```
output/building-expansion-20261109-v1/
├─ HANDOFF.md                # 总交接（面向人）
├─ DELIVERY-CHECKLIST.md     # 交付核对清单（冻结/dist/资产包/运行时实测）
├─ READ-ME-FIRST.md          # 三分钟导读（给评委）
├─ LANDMARKS.md              # 地标与填充记录
├─ SECOND-TIER.md            # 第二梯队 6 件记录 + 额度说明
├─ WALL-AUDIT.md             # 城墙范围与历史资料复核（含两条路线）
├─ WALL-REDESIGN.md          # 城墙五轮重做记录
├─ AUDIT-20261110.md / SHOWCASE.md / CURRENT.md
├─ build_preview.mjs         # 把 overlay 打到 build/（10 处 patch）
├─ integration-preview/
│  ├─ serve-preview.ps1      # 本地服务（必须 -NoBrowser）
│  ├─ Play-Windows.bat       # 一键进游戏
│  ├─ modules/               # 全部 overlay 模块（见下）
│  └─ build/                 # 派生 main.js/world.js/index.html + receipt.json
├─ package/                  # 资产包 + manifest + SHA256SUMS
├─ provider/                 # 群核 API 控制器、台账、产物
├─ review/<id>/              # 每件模型两视角审图
├─ report/                   # 六站导览册、改造对照
├─ offline-preview/          # 30 件候选交互预览 HTML
└─ PUBLISH/                  # 对外快照（已推 GitHub）
```

overlay 模块：

| 文件 | 作用 |
|---|---|
| `modules/building-expansion-preview.js` | 主控：候选加载/替换、屋顶三组拆分、色调统一、supplementary（地标/补充建筑）、导览、focusAnchor/focusPerson/**focusSupplementary**/debugSupplementaryBounds |
| `modules/street-surfaces.js` | 石板街、路缘、驳岸、木栏杆、城墙（夯土/垛口/马面/敌楼/战棚/护城河/瓮城） |
| `modules/tile-texture.js` | 程序化深灰瓦贴图 |
| `modules/earth-wall-texture.js` | 程序化夯土墙贴图 |
| `modules/density-fill.js` | 摊位/灯笼/郊野道具 |
| `modules/people-extra.js` | 12 个绑骨人物 + 行走 |
| `modules/life-scenes.js` | 4 簇生活场景 |
| `modules/infill.js` | 空地填充（院墙/草棚/水井/泊船/石阶/地面补丁/农舍） |
| `modules/fills.js` | 巷道/步道/土路/墙根小屋/河对岸民居/城东小院 |

---

## 3. 投稿前必须处理的事（按优先级）

### P0 — 必须做，否则不能提交
1. **决定投稿形态**（用户/lead 决策，Codex 不要擅自改 dist）：
   - 方案 A：提交冻结 r1 正式包 + 扩充资产包 + 预览/视频 + 说明（**当前状态即此方案**）；
   - 方案 B：把 overlay 合入 dist 出正式版（需要解冻 dist、重新打包、重跑验证、更新所有哈希）。**未经用户明确授权不得执行**。
2. **补一份"AI 生成声明与来源"专页**：说明模型由 Aholo/Lux3D（群核）text-to-3D / guided 生成、Blender 后处理、哪些是原画可见、哪些是推断；现成素材在 `provider/ledger.json`、`supplement-buildings/catalog.json`、`README`。
3. **资产包上传方式确认**：649 MB 超过 GitHub 单文件 100 MB 限制；投稿平台若限体积，需要网盘链接或 GitHub Release + LFS。**不要**直接把 zip 塞进仓库。
4. **录一段 3 分钟演示**：建议路线 = 郊野 → 汴河码头 → 虹桥 → 城门（看城墙/护城河）→ 城内街市（塔院、樊楼、脚店、香药铺）→ 城墙顶（说明暂不可行走）。素材可用 `integration-preview/Play-Windows.bat` 手动录，或按 `report/gallery-guide.html` 六站。
5. **人工验收**（Codex 无法替代）：
   - 塔院（塔 + 寺 + 学塾）、城门（城墙 12.4 m + 护城河）、河岸（民居甲 + 茶坊 + 州桥）三个机位；
   - 民居甲落点 (114,27)、学塾落点 (336,41) 偏离原定目标较远，是否可接受。

### P1 — 应该做，能明显提升观感
1. **地标位置微调**：民居甲、学塾、脚店（(186,30.5)，面阔仅 7.45 m 未达 12 m 目标）落点偏离；可在 `integration-preview/modules/building-expansion-preview.js` 的 supplementary target 里改目标点后重建。
2. **塔下合并桶穿模**：塔 (303.5,57) 与 `fills-*` 合并桶重叠（无法局部删除，已记录 `clearedProcedural.meshesKept`）；可在 `fills.js` 里对塔 footprint 做"生成期跳过"彻底解决。
3. **护城河真实开口**：现在只有 1 处真实缺口（x=210），主门洞外、码头步道处只有记录未挖开；`street-surfaces.js` 的 `moat.openings` 判断条件需要按"是否有沟"细化。
4. **瓮城已被整体放弃**（`barbican.dropped=true`，6 段里 5 段与既有墙相交）。若要恢复，建议**改小到 8×10 m 并整体外移**到不与 A05 门楼相交处，再逐段渲染未相交部分。
5. **屋顶瓦观感**：近看仍偏"贴图瓦"；可调 `tile-texture.js` 的瓦垄对比与 `replaceRoofFaces` 的坡度对齐。
6. **马道不可行走**：`world.floor` 是 dist 的地面高度查询，overlay 包裹会影响寻路/相机；已明确放弃。若用户授权改 dist 才能做。

### P2 — 可选
1. 再补 1–2 件建筑（余额仅 3 积分，不够；除非再放开额度）。
2. 六站导览册 `report/gallery-guide.html` 里的截图可换成最新（现在部分截图是 11-10 的）。
3. `offline-preview/buildings-selected.html` 仍是 30 件候选，可更新到 98 件全量。

---

## 4. 环境与命令速查

```powershell
# 工作目录
cd "C:\Users\derek\Documents\ChatGPT\清明上河图\output\building-expansion-20261109-v1"

# 重建 overlay（改完 modules/*.js 后必须做）
node build_preview.mjs

# 起本地服务（务必 -NoBrowser，否则会弹浏览器窗口）
Start-Process powershell -ArgumentList '-NoProfile','-ExecutionPolicy','Bypass','-File','.\integration-preview\serve-preview.ps1','-NoBrowser' -WindowStyle Hidden
# 端口写在 integration-preview/preview-runtime.json
# 关闭：读 preview-runtime.json 的 pid，Stop-Process

# 校验资产包
python verify_package.py
python make_asset_package.py     # 重算 manifest（新增模型后）
python make_asset_zip.py         # 重打 zip
python snapshot_public.py        # 刷新对外快照数据
python make_publish_snapshot.py  # 生成 PUBLISH/ 并提交

# 群核（若还需生成；余额仅 3，不要动）
python provider/controller.py account
python provider/controller.py reconcile
```

无头验收（Playwright 已装）：

```javascript
// node _check.mjs <url>
const {chromium}=await import('file:///C:/Users/derek/.cache/codex-runtimes/codex-primary-runtime/dependencies/node/node_modules/playwright/index.mjs');
// 进入后读：window.__BUILDING_EXPANSION_PREVIEW__.summary
// 再读 debugSupplementaryBounds() 看 14 件 supplementary 的实测世界尺寸
```

---

## 5. 硬约束（务必遵守）

1. **不得修改** `output/final-20261108/...r1-source.zip`、`dist/` 三文件、旧交付；它们的 SHA 见第 1 节，改了就说明越界。
2. **不得公开发布**：GitHub 仓库已存在且是用户授权推的，可以继续 push 文档；但**不要**把资产包/大 HTML 推到仓库，**不要**做任何"发布"动作（发帖、上架）。
3. **不要用 `sandbox_permissions`**（本会话审批已禁用，请求会直接被拒）。
4. 视觉/交互/性能的最终验收由用户人工完成；冒烟通过 ≠ 验收通过，报告里要写清楚"未验证项"。
5. 群核 API 余额 **3**，不要发起新的付费任务；确有需要必须先问用户。
6. 不要把候选说成"整卷精确复原"；地标位置、背面、进深、柱跨都是**推断**，`inferredPosition: true` 要保留。

---

## 6. 建议 Codex 接手后的第一步（10 分钟自检）

```
1) 核对第 1 节四个 SHA（dist 三个 + r1 + zip）——不一致就先查是谁改的，不要继续往下做
2) 起 -NoBrowser 服务 → 无头进场景 → 确认 summary.state === 'ready'、errors 为空、supplementary=14
3) 跑 debugSupplementaryBounds()，确认 14 件地标/建筑的 measured 尺寸与 SECOND-TIER.md/LANDMARKS.md 一致
4) 截图三个验收机位：塔院、城门、河岸（用 focusSupplementary('supp-pagoda-v1') 等）
5) 再开始 P0 里的“AI 生成声明”与投稿清单
```

---

## 7. 可直接粘给 Codex 的启动提示词

> 你在 `C:\Users\derek\Documents\ChatGPT\清明上河图` 工作，接手一个"清明上河图三维场景扩充"项目。
> 先读 `output/building-expansion-20261109-v1/HANDOFF-TO-CODEX.md`（本文件），再读同目录的 `DELIVERY-CHECKLIST.md`、`LANDMARKS.md`、`SECOND-TIER.md`、`WALL-REDESIGN.md`。
> 硬约束：不得修改 `output/final-20261108/` 下冻结包与 `dist/` 三文件（SHA 见文档第 1 节）；不得公开发布；群核余额仅 3，不要发起付费任务；不要请求沙箱提权。
> 当前任务：① 按第 6 节做 10 分钟自检并把结果写进 `CURRENT.md`；② 完成第 3 节 P0 的"AI 生成声明与来源"专页；③ 列出投稿所需文件清单并与实际文件核对；④ 输出一份"投稿前待办"给用户确认。不要改 dist，不要重新打包除非用户要求。
