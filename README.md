# jigsaw-data

Jigsaw Fox 拼图游戏的公开内容分发仓库（测试部署 v0.1.0）。

内容遵循 **studio v2.3.0 输出规范**（详见 jigsawpuzzle 项目 `studio/docs/unified-content-export-and-storage-architecture.md`）：

- 根入口 `manifest.json`（`schemaVersion: 4`），内部 URL 一律为 RFC 3986 相对路径，
  客户端以 manifest 自身 Base URI 递归解析，可平滑挂载于任意直连 / 加速 / 代理前缀下。
- 各模块容器键统一为 `items`。
- 主线关卡图片不可变，换图走补丁批次与 `-r{rev}` 修订；zip 归档为只读资产。

## 仓库结构

```
manifest.json              根路由清单（4 模块：main/daily/events/collections）
main/
  index.json               主线批次索引（Append-Only）
  batches/batch_001.json   关卡清单（id main:101..130，共 30 关）
  images/101.webp ~ 130.webp
daily/index.json           每日挑战月份索引（202607/202608/202609）
events/index.json + covers/   活动包 ×3（含双语标题/描述）
collections/index.json + covers/  合集包 ×5（category 取自 taxonomy 主 tag）
```

zip 整包不进入 git 仓库（避免仓库膨胀），全部作为 **GitHub Release 资产**发布：

- `https://github.com/mcxiaoke/jigsaw-data/releases/download/v0.1.0/{202607|202608|202609}.zip`
- `https://github.com/mcxiaoke/jigsaw-data/releases/download/v0.1.0/{evt_*}.zip`（3 个活动）
- `https://github.com/mcxiaoke/jigsaw-data/releases/download/v0.1.0/{col_*}.zip`（5 个合集）

`manifest.json` 内模块 URL 指向仓库内 `index.json`；zip 模块条目内的 `zipUrl` 为 Release 绝对 URL。

## 内容规模

| 模块 | 数量 | 说明 |
|---|---|---|
| main | 30 关 | tags 取自 taxonomy v3.2.0 预定义主 tag（测试数据，非真实标注） |
| daily | 3 个月 | 2026-07(31)/08(31)/09(30) 按日历天数 |
| events | 3 个 | 各 10 张 |
| collections | 5 个 | 各 10 张，category ∈ 主 tag |

## 访问通道（中国大陆网络）

仓库内 JSON/图片与 Release zip 的官方地址在国内可能不稳定，可按需套用加速前缀
（前缀会保留在 manifest 的 Base URI 上，由客户端 RFC 3986 递归解析，无需改内容）：

| 通道 | 形式 |
|---|---|
| 官方 raw | `https://raw.githubusercontent.com/mcxiaoke/jigsaw-data/master/manifest.json` |
| jsDelivr | `https://cdn.jsdelivr.net/gh/mcxiaoke/jigsaw-data@master/manifest.json` |
| gh 代理 | `https://<gh-proxy-host>/https://raw.githubusercontent.com/...` 或 `.../github.com/.../releases/download/...` |

> 注意：jsDelivr 仅加速仓库内文件，Release zip 请走官方地址或 gh 代理。

## 本地复现

部署与校验脚本位于 jigsawpuzzle 仓库 `scripts/deploy/`：

```bash
python scripts/deploy/prepare_assets.py   # 素材采样（Jigsaw_Organized → src_*）
python scripts/deploy/export_data.py      # studio 规范导出（out/ + publish_plan.json）
python scripts/deploy/validate_out.py     # 本地格式全量校验
python scripts/deploy/validate_remote.py  # 远端 URL / 代理连通巡检（发布后）
```

产物暂存于 `C:\Home\Temp\jigsawdata_build\`。
