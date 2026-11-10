# Blender 管线（对应参考文章里 "Opus 5.5 + Blender" 的部分）

本机已确认：**Blender 5.2.1 LTS**，路径 `C:\Program Files\Blender Foundation\Blender 5.2\blender.exe`；且 Blender 里装了 **BlenderMCP** 插件（启动日志可见 "BlenderMCP addon unregistered"）。本管线默认走**无头 CLI**，因为它可重复、可脚本化、不需要人来点按钮；需要交互时再挂 MCP。

## 参考文章里的分工，对应到本项目

| 文章里的角色 | 本项目里的做法 |
|---|---|
| Image 2.5 出场景/人物图 | 用《清明上河图》**原生像素裁图**作参考；人物另加可核实的人物类型描述 |
| Tripo 生成 3D 资产 | 用**群核 / Aholo Lux3D**（G1）生成建筑与人物——本项目已用同一账户生成 21 件建筑候选 + 3 个职业人物 |
| Opus 5.5 操作 Blender 优化/绑骨 | `blender-pipeline/prep_assets.py`（清理 + 等比缩放 + 贴地 + 可选减面 + 建骨架 + 自动权重 + Idle 动作 + 导出 GLB/.blend） |
| Opus 5.5 操作 Unity 搭场景 | 本项目不引入 Unity，沿用现有 Three.js 工程与 `integration-preview` 的 overlay 机制（不改 dist） |
| Auto-Rig Pro / Mixamo | **未安装 Auto-Rig Pro（付费插件）**，改用 Blender 内置的骨骼 + 自动权重；Mixamo 需要联网下载 FBX，本轮未用。要做走路/挑担等动作，可后续用 MCP 交互调 IK，或下载 Mixamo 动作再重定向 |

## 本轮已跑通（真实执行，不是设想）

1. **准备脚本**：`prep_assets.py`——导入 GLB → 应用变换 → 合并重复点 → 重算法线 → 等比缩放到目标身高（1.75 m）→ 底部贴地 → 可选减面 → 建 8 骨简化人形骨架（hips/spine/neck/head/arm.L/R/leg.L/R/shin）→ 自动权重绑定 → 生成 4 秒 Idle 动作（脊柱呼吸 + 头部轻摆，96 帧循环）→ 导出 skinned GLB + 保存 `.blend`。
2. **三个职业人物全部完成绑骨**：
   - `out/person-vendor-rigged.glb` / `.blend`
   - `out/person-porter-v1-rigged.glb` / `.blend`
   - `out/person-boatman-v1-rigged.glb` / `.blend`
   - 三件都是 **automatic-weights 绑定成功**，导出后仍 1 个 mesh、约 1.9 万三角、1 个 `Idle` 动画（4.0 s / 36 tracks）、包围盒高度 1.75 m。
3. **QA 渲染脚本**：`render_qa.py`——四视角转台 + 三点光，输出 640×640 PNG，用于检查绑定后有没有拉扯/穿模。示例：[摊贩四视角](<qa/vendor/vendor-view-01.png>)。

## 开源内容的边界（我的判断）

- **工具层**：Blender（GPL）、Three.js（MIT）、Playwright 都是开源，可放心用；我们已经在用。
- **资产层**：CC0/CC-BY 的模型与贴图可以借鉴，但要满足三点：① 许可证与署名可追溯；② 时代与画风必须与宋画一致（大部分免费资产是现代/西方风格，硬塞会更假）；③ 不能用"看起来像"的模型替换有原画依据的建筑。**本项目仍以原画裁图为准，不用外部资产替代。**
- **代码层**：可以借鉴开源的结构/实现思路，但本项目所有改动都在独立目录、未并入冻结版。

## 下一步（按参考文章继续）

1. 把三个**绑骨后**的人物替换掉现在场景里的静态版本（有 Idle 动画，人物会呼吸/轻摆）。
2. 若要"挑担走路/撑篙/招呼客人"这类动作：用 Blender MCP 交互调骨架 + 手 K 关键帧，或下载 Mixamo 动作后重定向；这属于新增动画工作，需另开一轮。
3. 建筑侧可用同一 Blender 管线做"减面 + 合并重复点 + 重算法线"的优化版，减小 GLB 体积后再进游戏。
