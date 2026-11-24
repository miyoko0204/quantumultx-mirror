# quantumultx-mirror
> 中文：quantumultx-mirror

Personal mirror of six public Quantumult X / Surge rule & script repos, so
Miyoko's Quantumult X config can pull rules and scripts from her own GitHub
account instead of directly from the original authors' repos.
> 中文：六个公开的 Quantumult X / Surge 规则与脚本仓库的个人镜像，使 Miyoko 的 Quantumult X 配置可以从她自己的 GitHub 账号拉取规则和脚本，而不必直接从原作者的仓库拉取。

> ⚠️ This repo mirrors **public upstream content only**. Do NOT add personal
> config files, proxy node subscriptions, MITM certificates (`.p12`) or
> passphrases here — those stay local only.
> 中文：⚠️ 本仓库仅镜像**公开的上游内容**。请勿在此添加个人配置文件、代理节点订阅、MITM 证书（`.p12`）或密码口令——这些仅保留在本地。

## Layout
> 中文：目录结构

| Folder | Upstream | Contents |
|---|---|---|
| `orz-3/` | [Orz-3/QuantumultX](https://github.com/Orz-3/QuantumultX) (`master` @ `a014cd9`) | `Orz-3.conf` base config, Bili/YouTube/TikTok configs, `Task/*.json` BoxJS subscriptions, `IP.js`, `Bili_Auto_Regions.js`<br>中文：基础配置 `Orz-3.conf`，Bili/YouTube/TikTok 配置，`Task/*.json` BoxJS 订阅，`IP.js`，`Bili_Auto_Regions.js` |
| `kop-xiao/` | [KOP-XIAO/QuantumultX](https://github.com/KOP-XIAO/QuantumultX) (`master` @ `69adf93`) | `Scripts/resource-parser.js`, `Scripts/streaming-ui-check.js` and other check scripts, `QuantumultX_Profiles.conf`, rule lists<br>中文：`Scripts/resource-parser.js`、`Scripts/streaming-ui-check.js` 及其他检测脚本，`QuantumultX_Profiles.conf`，规则列表 |
| `rulego/` | [ConnersHua/RuleGo](https://github.com/ConnersHua/RuleGo) (`master` @ `a7e5c4b`) | `Surge/Ruleset/*` domain lists (Direct/Proxy/AI/Streaming/Apple/Reject…), `Surge/Module/*`, `Quantumult/`, `Shadow/`<br>中文：`Surge/Ruleset/*` 域名列表（Direct/Proxy/AI/Streaming/Apple/Reject……），`Surge/Module/*`，`Quantumult/`，`Shadow/` |
| `blackmatrix7/` | [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) (`master` @ `0b6897d`) | Curated Quantumult X rules: core aggregates (AdvertisingLite, Privacy, EasyPrivacy, Global, China, Proxy…), Apple / AI / messaging / streaming / service lists, `rewrite/QuantumultX/`, `external/QuantumultX/`, scripts, `source/`<br>中文：精选的 Quantumult X 规则：核心聚合规则（AdvertisingLite、Privacy、EasyPrivacy、Global、China、Proxy……），Apple / AI / 消息 / 流媒体 / 服务列表，`rewrite/QuantumultX/`，`external/QuantumultX/`，脚本，`source/` |
| `ddgksf2013/` | [ddgksf2013/ddgksf2013](https://github.com/ddgksf2013/ddgksf2013) (`main` @ `dd96698`) | `README.md`, `Profile/QuantumultX.conf`, `filter/Ai.yaml` — see note below<br>中文：`README.md`、`Profile/QuantumultX.conf`、`filter/Ai.yaml`——见下方说明 |
| `sukkaw/` | [SukkaW/Surge](https://github.com/SukkaW/Surge) (`master` @ `cb2876a`) | `Source/` rule inputs (AI, reject, streaming…), `Modules/`, plus built lists under `List/` fetched from `ruleset.skk.moe`<br>中文：`Source/` 规则输入（AI、reject、流媒体……），`Modules/`，以及从 `ruleset.skk.moe` 获取的 `List/` 下已构建列表 |

Mirrored 2026-11-17 (first three folders) and 2026-11-18 (last three folders).
See `ANALYSIS.md` for the prune / supplement plan (what to keep, remove and
add — approval needed before any pruning).
> 中文：于 2026-11-17 镜像（前三个文件夹）和 2026-11-18 镜像（后三个文件夹）。关于精简/补充计划（保留、删除和新增的内容——精简前需批准），见 `ANALYSIS.md`。

## Licences
> 中文：许可证

Upstream licences are kept with the mirrored content where they exist:
> 中文：上游许可证随镜像内容一并保留（如有）：

- `blackmatrix7/` — **GPL-2.0** (`blackmatrix7/LICENSE`). Private use is
  fine; republishing or distributing modified versions triggers copyleft.
  > 中文：`blackmatrix7/`——**GPL-2.0**（`blackmatrix7/LICENSE`）。个人使用没问题；重新发布或分发修改版本会触发 copyleft（著佐权）条款。
- `sukkaw/` — **AGPL-3.0** (`sukkaw/LICENSE`). Same caution as above.
  > 中文：`sukkaw/`——**AGPL-3.0**（`sukkaw/LICENSE`）。与上述相同，需谨慎。
- `ddgksf2013/` — **no licence** shown in the upstream repo.
  > 中文：`ddgksf2013/`——上游仓库中未显示任何许可证。
- `orz-3/`, `kop-xiao/`, `rulego/` — no licence files in the mirrored
  upstream content.
  > 中文：`orz-3/`、`kop-xiao/`、`rulego/`——镜像的上游内容中没有许可证文件。

## Notes
> 中文：说明

- RuleGo's **default branch is a lookalike branch `maѕter`** (Cyrillic "ѕ")
  containing only a README. The real content lives on branch **`master`** —
  this mirror tracks `master`.
  > 中文：RuleGo 的**默认分支是一个形似 `master` 的分支 `maѕter`**（西里尔字母“ѕ”），其中只有一个 README。真正的内容在 **`master`** 分支上——本镜像跟踪 `master` 分支。
- `kop-xiao/img/` PNG icons (policy icons, dragonball/southpark images) are
  **not** in this mirror: the GitHub connector's snapshot push accepts
  UTF-8 text files only, no binaries. They are decorative only and are not
  referenced by the Quantumult X config. To add them, push from a local
  `git` checkout with your own GitHub credentials.
  > 中文：`kop-xiao/img/` 中的 PNG 图标（策略图标、龙珠/南方公园图片）**不在**本镜像中：GitHub 连接器的快照推送仅接受 UTF-8 文本文件，不接受二进制文件。它们仅起装饰作用，且未被 Quantumult X 配置引用。如需添加，请使用自己的 GitHub 凭据从本地 `git` 检出推送。
- `Orz-3/mini` (policy icons referenced by `img-url=` in the config) is a
  separate repo and is **not** mirrored yet — see `ANALYSIS.md`.
  > 中文：`Orz-3/mini`（配置中 `img-url=` 引用的策略图标）是一个独立仓库，**尚未**镜像——见 `ANALYSIS.md`。
- **blackmatrix7 is curated, not complete.** The upstream Quantumult X rule
  set alone is ~64 MB / 1,376 files. This mirror keeps the core aggregates
  and ~120 selected service lists (~11 MB). Excluded: the ~600 other
  per-service lists, the giant single files `Advertising` (12 MB),
  `AdvertisingTest` (14 MB), `ChinaMax*` / `ChinaTest` / `AdGuardSDNSFilter`
  (4–5 MB each), `icon/` PNGs, `script/archive/`, and the Clash / Loon /
  Shadowrocket / Surge / AdGuard variants of the same rules.
  > 中文：**blackmatrix7 经过精选，并非完整镜像。**仅上游 Quantumult X 规则集就有约 64 MB / 1,376 个文件。本镜像保留核心聚合规则和约 120 个精选服务列表（约 11 MB）。已排除：约 600 个其他按服务划分的列表、单个大文件 `Advertising`（12 MB）、`AdvertisingTest`（14 MB）、`ChinaMax*` / `ChinaTest` / `AdGuardSDNSFilter`（各 4–5 MB）、`icon/` PNG 图片、`script/archive/`，以及相同规则的 Clash / Loon / Shadowrocket / Surge / AdGuard 变体。
- **ddgksf2013 is partial.** Its GitHub repo contains only a README; the
  actual files live on the author's personal site `ddgksf2013.top`. That
  site currently returns its HTML homepage instead of the files for
  `rewrite/*.conf` and `scripts/*.js`, so those could not be mirrored, and
  the gist links in the README return 404. Only the two files that download
  correctly are mirrored: `Profile/QuantumultX.conf` and `filter/Ai.yaml`.
  The upstream URLs still work as Quantumult X remote references if the
  site serves them to the app.
  > 中文：**ddgksf2013 仅为部分镜像。**其 GitHub 仓库中只有一个 README；实际文件位于作者个人网站 `ddgksf2013.top`。该网站目前对 `rewrite/*.conf` 和 `scripts/*.js` 返回的是 HTML 首页而非文件，因此这些文件无法镜像，README 中的 gist 链接返回 404。仅镜像了能正常下载的两个文件：`Profile/QuantumultX.conf` 和 `filter/Ai.yaml`。如果该网站能向应用提供文件，上游 URL 仍可作为 Quantumult X 远程引用使用。
- **sukkaw `List/` files are built outputs**, generated by the upstream CI
  and published at `ruleset.skk.moe` — they are not stored in the upstream
  git repo. They are mirrored here as fetched on 2026-11-18. `Source/` is
  the upstream git content the lists are built from. `Build/` (TypeScript
  build tooling), `.github/` and `Mock/` are not mirrored.
  > 中文：**sukkaw 的 `List/` 文件是构建产物**，由上游 CI 生成并发布于 `ruleset.skk.moe`——它们并不存储在上游 git 仓库中。它们按 2026-11-18 获取时的状态镜像于此。`Source/` 是用于构建这些列表的上游 git 内容。`Build/`（TypeScript 构建工具）、`.github/` 和 `Mock/` 未镜像。
- Upstream updates are pulled on a schedule (see the `quantumultx-mirror-sync`
  cron); the mirror does not auto-update in real time.
  > 中文：上游更新按计划拉取（见 `quantumultx-mirror-sync` 定时任务）；镜像不会实时自动更新。

## Use in Quantumult X
> 中文：在 Quantumult X 中使用

Replace upstream raw URLs with your own mirror, e.g.:
> 中文：将上游原始 URL 替换为你自己的镜像，例如：

- `https://raw.githubusercontent.com/ConnersHua/RuleGo/master/Surge/Ruleset/Proxy.list`
  > 中文：上游原始链接
- → `https://raw.githubusercontent.com/miyoko0204/quantumultx-mirror/main/rulego/Surge/Ruleset/Proxy.list`
  > 中文：替换为镜像链接

(Replace `main` with the repo's actual default branch if it differs.)
> 中文：（如有不同，请将 `main` 替换为该仓库实际的默认分支。）
