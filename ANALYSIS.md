# Quantumult X Mirror — Analysis & Pruning Plan
> 中文：Quantumult X 镜像——分析与精简计划

> Repo: `miyoko0204/quantumultx-mirror`
> 中文：仓库：`miyoko0204/quantumultx-mirror`
> Upstreams (cloned 2026-11-17, all on branch `master`):
> 中文：上游（2026-11-17 克隆，均位于 `master` 分支）：
> - `orz-3/` ← Orz-3/QuantumultX @ `a014cd9` — 32 files, ~98 KB
> 中文：`orz-3/` ← Orz-3/QuantumultX @ `a014cd9`——32 个文件，约 98 KB
> - `kop-xiao/` ← KOP-XIAO/QuantumultX @ `cce2996` — 100 files, ~1.1 MB
> 中文：`kop-xiao/` ← KOP-XIAO/QuantumultX @ `cce2996`——100 个文件，约 1.1 MB
> - `rulego/` ← ConnersHua/RuleGo @ `a7e5c4b` — 130 files, ~131 KB
> 中文：`rulego/` ← ConnersHua/RuleGo @ `a7e5c4b`——130 个文件，约 131 KB
> Local reference config: `quantumult_20261117220356.conf`
> 中文：本地参考配置：`quantumult_20261117220356.conf`
>   (= `orz-3/Orz-3.conf` + 13 local Muse/Meta rules + local MITM cert — see §5)
> 中文：（= `orz-3/Orz-3.conf` + 13 条本地 Muse/Meta 规则 + 本地 MITM 证书——见 §5）
> Status: **DRAFT — nothing in this plan has been executed. Every removal
> below needs Miyoko's approval.**
> 中文：状态：**草案——本计划中的任何内容均未执行。每项删除都需要 Miyoko 批准。**

## 0. How the local config actually uses the mirror
> 中文：0. 本地配置实际如何使用该镜像

Only a small fraction of the mirrored files is referenced:
> 中文：只有一小部分镜像文件被引用：

| Used by local config<br>中文：本地配置使用的内容 | Source in mirror<br>中文：镜像中的来源 |
|---|---|
| `resource-parser.js` (`[general]`, via jsDelivr) | `kop-xiao/Scripts/resource-parser.js` |
| `streaming-ui-check.js` (`[task_local]`) | `kop-xiao/Scripts/streaming-ui-check.js` |
| `IP.js` (`geo_location_checker`, stale URL — see §4)<br>中文：过期的 URL——见 §4 | `orz-3/IP.js` |
| 14 × `filter_remote` | `rulego/Surge/Ruleset/…` (Direct, Direct+, Proxy, Proxy+, AI, Apple/Apple, Telegram, WeChat, GoogleVoice, TikTok, Streaming `!CN` + `CN`, Reject Advertising + Tracking) |
| 1 of 4 × `rewrite_remote`<br>中文：4 个中的 1 个 | `rulego/Surge/Module/Block/Advertising.sgmodule` |
| 13 policy icons + profile image<br>中文：13 个策略图标 + 个人资料图片 | `Orz-3/mini` — **NOT mirrored yet** (§4)<br>中文：**尚未镜像**（§4） |

Everything else in the three folders is currently unused ballast or
optional material. The rest of this file sorts it into keep / prune / add.
> 中文：三个文件夹中的其余内容目前都是未使用的累赘或可选材料。本文其余部分将其分为保留 / 精简 / 新增三类。

## 1. What to keep
> 中文：1. 保留什么

### 1.1 rulego/ — keep the 14 lists the config loads, plus references
> 中文：1.1 rulego/——保留配置加载的 14 个列表及参考文件
- `Surge/Ruleset/Direct.list` (32 lines) and `Direct+.list` (28 lines):
  **complementary, zero domain overlap.** `Direct+` is corrections
  (Google `dl/update.googleapis.com`, Umeng, Strava analytics, Tencent live).
  Keep both, keep the order (Direct+ first — matches RuleGo's own
  `Quantumult/Sample.conf`).
  > 中文：`Surge/Ruleset/Direct.list`（32 行）与 `Direct+.list`（28 行）：**互补，域名零重叠。**`Direct+` 是修正项（Google `dl/update.googleapis.com`、友盟、Strava 分析、腾讯直播）。两者都保留，保持顺序（Direct+ 在前——与 RuleGo 自身的 `Quantumult/Sample.conf` 一致）。
- `Surge/Ruleset/Proxy.list` (565 lines) and `Proxy+.list` (2 lines):
  keep both. `Proxy+` is a single rule (`GEOIP,GOOGLE`); it is trivial but
  harmless, and it sits last in both the local config and upstream's sample.
  Do **not** reorder without testing — Quantumult X is first-match-wins.
  > 中文：`Surge/Ruleset/Proxy.list`（565 行）与 `Proxy+.list`（2 行）：两者都保留。`Proxy+` 只有一条规则（`GEOIP,GOOGLE`）；它微不足道但无害，且在本地配置和上游示例中都排在最后。**不要**在未经测试的情况下重新排序——Quantumult X 是先匹配先生效。
- `Surge/Ruleset/Extra/AI.list` (117 lines, 75 domains): keep as the single
  AI list. It already covers `muse.ai`, `meta.ai`, `meta.com`,
  `facebook.com`, `fbcdn.net`, `graph.facebook.com` (see §5).
  > 中文：`Surge/Ruleset/Extra/AI.list`（117 行，75 个域名）：作为唯一的 AI 列表保留。它已覆盖 `muse.ai`、`meta.ai`、`meta.com`、`facebook.com`、`fbcdn.net`、`graph.facebook.com`（见 §5）。
- `Surge/Ruleset/Extra/Apple/Apple.list` (20 lines): keep as the single
  Apple list. Its `DOMAIN-SUFFIX` umbrella (`apple.com`, `icloud.com`,
  `mzstatic.com`, `apple.com.cn` …) already covers most Apple subdomains.
  > 中文：`Surge/Ruleset/Extra/Apple/Apple.list`（20 行）：作为唯一的 Apple 列表保留。它的 `DOMAIN-SUFFIX` 兜底规则（`apple.com`、`icloud.com`、`mzstatic.com`、`apple.com.cn`……）已覆盖大多数 Apple 子域名。
- `Surge/Ruleset/Extra/Reject/Advertising.list` (1,077 lines) and
  `Tracking.list` (109 lines): keep both. Overlap is only 3 domains
  (`growingio.com`, `quantserve.com`, `socdm.com`) — they are complementary,
  not duplicates.
  > 中文：`Surge/Ruleset/Extra/Reject/Advertising.list`（1,077 行）与 `Tracking.list`（109 行）：两者都保留。重叠仅 3 个域名（`growingio.com`、`quantserve.com`、`socdm.com`）——它们是互补关系，不是重复。
- `Surge/Ruleset/Extra/Streaming/!CN.list` (303 lines) and `CN.list`
  (18 lines): keep both, zero overlap with each other.
  > 中文：`Surge/Ruleset/Extra/Streaming/!CN.list`（303 行）与 `CN.list`（18 行）：两者都保留，彼此零重叠。
- `Surge/Ruleset/Extra/Telegram.list`, `WeChat.list`,
  `Google/GoogleVoice.list`, `Streaming/Video/TikTok.list`: keep — each is
  small and directly referenced by the config.
  > 中文：`Surge/Ruleset/Extra/Telegram.list`、`WeChat.list`、`Google/GoogleVoice.list`、`Streaming/Video/TikTok.list`：保留——每个都很小，且都被配置直接引用。
- `Surge/Module/Block/Advertising.sgmodule`: keep — it is one of the 4
  `rewrite_remote` entries. Note it is Surge-format; Quantumult X parses it
  via `resource-parser.js`.
  > 中文：`Surge/Module/Block/Advertising.sgmodule`：保留——它是 4 个 `rewrite_remote` 条目之一。注意它是 Surge 格式；Quantumult X 通过 `resource-parser.js` 解析它。
- `Quantumult/Sample.conf` (206 lines) + `Quantumult/Script/geo_location_checker.js`:
  keep **as reference only**. Sample.conf is the cleanest curated-config
  template available (same filter order as the local config) and its
  geo-location script is the maintained replacement for the broken Orz-3
  IP.js URL (§4). Do not activate Sample.conf as-is.
  > 中文：`Quantumult/Sample.conf`（206 行）+ `Quantumult/Script/geo_location_checker.js`：**仅作为参考**保留。Sample.conf 是现有的最干净的精选配置模板（过滤器顺序与本地配置相同），其地理位置脚本是已损坏的 Orz-3 IP.js URL 的有人维护的替代品（§4）。不要按原样激活 Sample.conf。

### 1.2 kop-xiao/ — keep only what the config needs
> 中文：1.2 kop-xiao/——只保留配置需要的部分
- `Scripts/resource-parser.js` (266 KB, 5,740 lines): **required** — every
  `filter_remote`/`rewrite_remote` uses `opt-parser=true`. Keep, but see
  security note (§6): pin it, review diffs on update.
  > 中文：`Scripts/resource-parser.js`（266 KB，5,740 行）：**必需**——每个 `filter_remote`/`rewrite_remote` 都使用 `opt-parser=true`。保留，但见安全说明（§6）：固定版本，更新时检查差异。
- `Scripts/streaming-ui-check.js` (614 lines): keep — the only `[task_local]`
  entry. Fix its `policy_name = "Netflix"` first: the local config has **no**
  `Netflix` policy group, so the check currently has nowhere to report to
  (§4).
  > 中文：`Scripts/streaming-ui-check.js`（614 行）：保留——唯一的 `[task_local]` 条目。先修复其 `policy_name = "Netflix"`：本地配置**没有** `Netflix` 策略组，因此该检测目前无处上报（§4）。
- `img/southpark/7.png`: keep — it is the `media-url` thumbnail hard-coded
  in `streaming-ui-check.js` and 8 sibling check scripts. (Note: PNGs are
  not in the mirror yet — the connector snapshot push accepts text only.
  See README.md.)
  > 中文：`img/southpark/7.png`：保留——它是 `streaming-ui-check.js` 和 8 个同类检测脚本中硬编码的 `media-url` 缩略图。（注意：PNG 尚未进入镜像——连接器快照推送仅接受文本。见 README.md。）
- `README.md`, `QuantumultX_Profiles.conf`: keep as reference (commented
  template; not loaded by the config).
  > 中文：`README.md`、`QuantumultX_Profiles.conf`：作为参考保留（带注释的模板；未被配置加载）。

### 1.3 orz-3/ — keep the base config and the two scripts it points to
> 中文：1.3 orz-3/——保留基础配置及其指向的两个脚本
- `Orz-3.conf`: keep — it **is** the local config minus local additions.
  > 中文：`Orz-3.conf`：保留——它**就是**本地配置减去本地新增内容。
- `IP.js` (12 lines, parses ip-api.com JSON): keep, but its URL in the
  config is stale (§4).
  > 中文：`IP.js`（12 行，解析 ip-api.com JSON）：保留，但其在配置中的 URL 已过期（§4）。
- `Bili_Auto_Regions.js` + `Bili_Auto_Regions.conf`: keep **only if**
  Miyoko watches Bilibili HK/TW番剧; otherwise prune with the other
  media confs below.
  > 中文：`Bili_Auto_Regions.js` + `Bili_Auto_Regions.conf`：**仅当** Miyoko 观看 Bilibili 港台番剧时保留；否则与下面的其他媒体配置一同精简。

## 2. What to remove / prune (approval needed for each)
> 中文：2. 删除 / 精简的内容（每项都需要批准）

### 2.1 rulego/ — whole subtrees the config never loads
> 中文：2.1 rulego/——配置从不加载的整个子树
- `Surge/Ruleset/Extra/GenAI/` (13 files, 64 domains combined):
  **PRUNE as separate lists.** Measured overlap: all 64 GenAI domains are
  already inside `AI.list` (64/64). `AI.list` only adds 11 more
  (Apple Intelligence relay hosts, `openrouter.ai`, `zed.dev`). Keeping
  GenAI/* would only be useful if Miyoko wants a *per-vendor* AI policy
  (e.g. Anthropic → US node, Meta → US node separately) — she does not
  today. Delete, or move to `archive/` if she wants the split later.
  > 中文：`Surge/Ruleset/Extra/GenAI/`（13 个文件，共 64 个域名）：**作为独立列表精简。**实测重叠：全部 64 个 GenAI 域名都已包含在 `AI.list` 中（64/64）。`AI.list` 仅多出 11 个（Apple Intelligence 中继主机、`openrouter.ai`、`zed.dev`）。保留 GenAI/* 只有在 Miyoko 想要*按厂商区分* AI 策略时才有用（例如 Anthropic → 美国节点、Meta → 美国节点分开）——她目前不需要。删除，或移到 `archive/` 以备她日后想要拆分。
- `Surge/Ruleset/Extra/Streaming/Video/` (38 files), `Music/` (9),
  `Podcast/` (2), `Live/Twitch.list`: **PRUNE.** `!CN.list` is a superset
  of 37 of the 38 Video lists (100% of each list's domains; only
  `Youku.list` 0/13 and `Prime-Video.list` 14/15 are exceptions) and of
  Music 15/18, Podcast 2/2, Live 6/6. Consequence: `TikTok.list`
  (10/10 domains already in `!CN.list`) is redundant as a *list*, but the
  config loads it **before** `!CN.list` so TikTok still routes to 美国节点
  while other streaming goes to 国际媒体 — that ordering trick is the only
  reason to keep `TikTok.list` itself. The other ~48 files add nothing.
  > 中文：`Surge/Ruleset/Extra/Streaming/Video/`（38 个文件）、`Music/`（9 个）、`Podcast/`（2 个）、`Live/Twitch.list`：**精简。**`!CN.list` 是 38 个 Video 列表中 37 个的超集（每个列表的域名 100% 包含；仅 `Youku.list` 0/13 和 `Prime-Video.list` 14/15 是例外），也是 Music 15/18、Podcast 2/2、Live 6/6 的超集。结果：`TikTok.list`（10/10 个域名已在 `!CN.list` 中）作为*列表*是多余的，但配置在 `!CN.list` **之前**加载它，因此 TikTok 仍走美国节点，而其他流媒体走国际媒体——这个顺序技巧是保留 `TikTok.list` 本身的唯一理由。其余约 48 个文件毫无作用。
- `Surge/Ruleset/Extra/Apple/` sublists (17 files, 73 domains combined):
  **PRUNE as separate lists.** Only 10 domains overlap `Apple.list`, but
  the other 63 are overwhelmingly subdomains already covered by
  `Apple.list`'s suffix rules (`apple.com`, `icloud.com`, `mzstatic.com`).
  The genuinely additive entries are a handful of hosts
  (`appldnld.apple.com`, `swdownload.apple.com`, `testflight.apple.com`,
  `gateway.icloud.com`, …) plus Surge-only `PROCESS-NAME`/`USER-AGENT`
  rules (`TV`, `Music`, `AppleTV*`) that have no Quantumult X equivalent
  and are silently ineffective in QX — verify before relying on them.
  If Miyoko ever splits Apple services by policy, re-add selectively.
  > 中文：`Surge/Ruleset/Extra/Apple/` 子列表（17 个文件，共 73 个域名）：**作为独立列表精简。**只有 10 个域名与 `Apple.list` 重叠，但其余 63 个绝大多数是已被 `Apple.list` 后缀规则（`apple.com`、`icloud.com`、`mzstatic.com`）覆盖的子域名。真正新增的条目只有少数主机（`appldnld.apple.com`、`swdownload.apple.com`、`testflight.apple.com`、`gateway.icloud.com`……）以及仅 Surge 支持的 `PROCESS-NAME`/`USER-AGENT` 规则（`TV`、`Music`、`AppleTV*`），它们没有 Quantumult X 等价物，在 QX 中静默失效——依赖它们之前请先验证。如果 Miyoko 某天按策略拆分 Apple 服务，再有选择地加回。
- `Surge/Ruleset/Extra/Google/` (except `GoogleVoice.list`),
  `Extra/Microsoft/`, `Extra/PayPal.list`, `Extra/X.list`,
  `Extra/Crypto.list` (49 rules), `Extra/GeoLoc.list` (21 rules):
  **PRUNE by default.** Zero overlap with the lists the config uses
  (e.g. `GoogleDrive.list` shares 0 domains with `Google.list`), so they
  are not duplicates — they are just unused scope. `Scholar.list` is the
  one exception to consider adding (§3).
  > 中文：`Surge/Ruleset/Extra/Google/`（除 `GoogleVoice.list` 外）、`Extra/Microsoft/`、`Extra/PayPal.list`、`Extra/X.list`、`Extra/Crypto.list`（49 条规则）、`Extra/GeoLoc.list`（21 条规则）：**默认精简。**与配置使用的列表零重叠（例如 `GoogleDrive.list` 与 `Google.list` 共享 0 个域名），所以它们不是重复项——只是未使用的范围。`Scholar.list` 是唯一值得考虑新增的例外（§3）。
- `Surge/Profile.conf`, `Surge/README.md`: **PRUNE** — Surge app config,
  not Quantumult X.
  > 中文：`Surge/Profile.conf`、`Surge/README.md`：**精简**——Surge 应用配置，不是 Quantumult X。
- `Shadow/` (`Profile.conf`, `README.md`): **PRUNE** — Shadowrocket
  config, not Quantumult X.
  > 中文：`Shadow/`（`Profile.conf`、`README.md`）：**精简**——Shadowrocket 配置，不是 Quantumult X。
- `Surge/Module/` except `Block/Advertising.sgmodule` (i.e. `Block/`
  `AwemeAds`, `YouTubeAds`, `KindleUpdate`, `iOSUpdate`; `Unlock/`
  Netflix, bilibili ×2, AbeamTV; `Fix/` AirPlay, DDPai, Imaging-Edge;
  `FCM`, `GeoLoc`, `Hide-VPN-Icon`): **PRUNE by default.** They are
  Surge modules for unlocking/ad-blocking specific apps. Only re-add one
  if Miyoko asks for that app (e.g. `Unlock/Netflix.sgmodule` if she
  wants Netflix unlocking, `Block/iOSUpdate.sgmodule` to block OTA).
  > 中文：除 `Block/Advertising.sgmodule` 外的 `Surge/Module/`（即 `Block/` 的 `AwemeAds`、`YouTubeAds`、`KindleUpdate`、`iOSUpdate`；`Unlock/` 的 Netflix、bilibili ×2、AbeamTV；`Fix/` 的 AirPlay、DDPai、Imaging-Edge；`FCM`、`GeoLoc`、`Hide-VPN-Icon`）：**默认精简。**它们是用于解锁/屏蔽特定应用广告的 Surge 模块。只有 Miyoko 要求某个应用时才重新添加一个（例如想要 Netflix 解锁时加 `Unlock/Netflix.sgmodule`，屏蔽 OTA 时加 `Block/iOSUpdate.sgmodule`）。
- `Surge/Script/bstar.js`: **PRUNE** — single Surge script, unused.
  > 中文：`Surge/Script/bstar.js`：**精简**——单个未使用的 Surge 脚本。

### 2.2 kop-xiao/ — the bulk is icons and unused check scripts
> 中文：2.2 kop-xiao/——大部分是图标和未使用的检测脚本
- `img/dragonball/` (34 files, ~428 KB): **PRUNE.** Zero references in any
  script, config or JSON in the repo. Pure decoration.
  > 中文：`img/dragonball/`（34 个文件，约 428 KB）：**精简。**仓库中任何脚本、配置或 JSON 均未引用。纯装饰。
- `img/policy/` (17 files, ~120 KB): **PRUNE unless** Miyoko wants to
  switch policy icons from Orz-3/mini to these. The local config uses
  Orz-3/mini `Color/*.png`, never these.
  > 中文：`img/policy/`（17 个文件，约 120 KB）：**精简，除非** Miyoko 想把策略图标从 Orz-3/mini 换成这些。本地配置使用 Orz-3/mini 的 `Color/*.png`，从不用这些。
- `img/southpark/` except `7.png` (9 files, ~130 KB): **PRUNE.** Only
  `7.png` is referenced (by 9 check scripts). Keep `7.png`, drop 1–6, 8–10.
  > 中文：除 `7.png` 外的 `img/southpark/`（9 个文件，约 130 KB）：**精简。**只有 `7.png` 被引用（被 9 个检测脚本引用）。保留 `7.png`，删除 1–6、8–10。
- `img/sub/Dler.png`, `img/others/lee.png`: **PRUNE** — unreferenced.
  > 中文：`img/sub/Dler.png`、`img/others/lee.png`：**精简**——未被引用。
- `Scripts/` unused (25 of 27 scripts): **PRUNE or move to `archive/`.**
  Only `resource-parser.js` and `streaming-ui-check.js` are loaded.
  Unused: 9 streaming-check variants (`nfcheck.js`, `ytbcheck.js`,
  `nf-ui-check.js`, `nf_ytb_ui-check.js`, `ytb-ui-check.js`,
  `disney-ui-check.js`, `switch-check-{nf,ytb,disneyplus,google}.js`),
  7 IP variants (`IPCheck/IPConfig/IPCustom/IPInfo/IP_API/IP_bili/IP_bili_cn.js`),
  `traffic-check.js`, `server-info-plus.js`, `network-direct.js`,
  `geo_location.js`. Note `switch-check-*.js` are only useful together
  with `Scripts/Cron-Switch.json` (auto-switch policy by unlock status) —
  prune as a set, or keep as a set if Miyoko wants auto-switching.
  > 中文：未使用的 `Scripts/`（27 个脚本中的 25 个）：**精简或移到 `archive/`。**只有 `resource-parser.js` 和 `streaming-ui-check.js` 被加载。未使用的有：9 个流媒体检测变体（`nfcheck.js`、`ytbcheck.js`、`nf-ui-check.js`、`nf_ytb_ui-check.js`、`ytb-ui-check.js`、`disney-ui-check.js`、`switch-check-{nf,ytb,disneyplus,google}.js`）、7 个 IP 变体（`IPCheck/IPConfig/IPCustom/IPInfo/IP_API/IP_bili/IP_bili_cn.js`）、`traffic-check.js`、`server-info-plus.js`、`network-direct.js`、`geo_location.js`。注意 `switch-check-*.js` 只有与 `Scripts/Cron-Switch.json`（按解锁状态自动切换策略）一起才有用——要么作为一组精简，要么如果 Miyoko 想要自动切换就作为一组保留。
- `Scripts/TaskGallery.json` (HotKids gallery, ~11 KB), `Scripts/UI-Action.json`,
  `Scripts/emoji.json`, `Scripts/Cron-Switch.json`: **PRUNE by default**
  (reference material; TaskGallery points at third-party repos incl. a
  Gitee host — see §6).
  > 中文：`Scripts/TaskGallery.json`（HotKids 图库，约 11 KB）、`Scripts/UI-Action.json`、`Scripts/emoji.json`、`Scripts/Cron-Switch.json`：**默认精简**（参考材料；TaskGallery 指向第三方仓库，包括一个 Gitee 托管——见 §6）。
- `HotKids-JS/scripts.conf`: **PRUNE** — empty file.
  > 中文：`HotKids-JS/scripts.conf`：**精简**——空文件。
- `Q-Search.list`, `TikTok_Rewrite.list`: **PRUNE by default** — Safari
  search-shortcut rewrites and a TikTok region rewrite (`CN → JP`,
  version pin `8.4.0`) that duplicate `orz-3/TikTok.conf`. Keep only if
  Miyoko uses them; note the TikTok version pin is years out of date.
  > 中文：`Q-Search.list`、`TikTok_Rewrite.list`：**默认精简**——Safari 搜索快捷方式重写和 TikTok 地区重写（`CN → JP`，版本锁定 `8.4.0`），与 `orz-3/TikTok.conf` 重复。只有 Miyoko 使用它们时才保留；注意 TikTok 版本锁定已过期多年。
- `QuanX-RuleSet-Divine-Engine.json`, `remote-resource.json`:
  **PRUNE.** Both point at dead/stale upstreams
  (`ConnersHua/Profiles`, `cloudcompute.lbyczf.com` proxy rewrites,
  `gist/.../Fndroid`) and at `crossutility/Quantumult-X` sample servers.
  Do not load either.
  > 中文：`QuanX-RuleSet-Divine-Engine.json`、`remote-resource.json`：**精简。**两者都指向已失效/过期的上游（`ConnersHua/Profiles`、`cloudcompute.lbyczf.com` 代理重写、`gist/.../Fndroid`）和 `crossutility/Quantumult-X` 示例服务器。不要加载其中任何一个。
- `.DS_Store` × 5 (`kop-xiao/`, `img/`, `img/southpark/`, `Scripts/`,
  `HotKids-JS/`): **DELETE.** macOS Finder junk, no function.
  > 中文：5 个 `.DS_Store`（`kop-xiao/`、`img/`、`img/southpark/`、`Scripts/`、`HotKids-JS/`）：**删除。**macOS Finder 垃圾文件，没有作用。

### 2.3 orz-3/ — the Task gallery and cookie-capture confs
> 中文：2.3 orz-3/——Task 图库与 Cookie 捕获配置
- `Task/*.json` (22 files, ~200 cron entries, ~104 KB total):
  **PRUNE by default; keep at most 1–2 authors if Miyoko uses them.**
  These are daily sign-in/check-in automations for JD, Weibo, Bilibili,
  iQIYI, carriers, etc., pointing at third-party repos (NobyDa,
  chavyleung, Sunert, Peng-YM, …). Quality issue: **20 of 22 files fail
  strict JSON parsing** (trailing commas — e.g. `Nobyda.json`,
  `chavyleung.json`); only `evilbutcher.json` and `photonmang.json`
  parse cleanly. Verify in Quantumult X before importing any of them.
  No embedded tokens/cookies were found in the JSON files themselves —
  the risk is the *scripts they load*, which read cookies from QX storage
  (see §6).
  > 中文：`Task/*.json`（22 个文件，约 200 条 cron 条目，共约 104 KB）：**默认精简；如果 Miyoko 使用，最多保留 1–2 位作者的。**这些是京东、微博、哔哩哔哩、爱奇艺、运营商等每日签到/打卡自动化，指向第三方仓库（NobyDa、chavyleung、Sunert、Peng-YM……）。质量问题：**22 个文件中有 20 个无法通过严格 JSON 解析**（尾随逗号——例如 `Nobyda.json`、`chavyleung.json`）；只有 `evilbutcher.json` 和 `photonmang.json` 能干净解析。在 Quantumult X 中导入任何一个之前请先验证。JSON 文件本身未发现内嵌的 token/cookie——风险在于*它们加载的脚本*，这些脚本会读取 QX 存储中的 cookie（见 §6）。
- `JS_GetCookie.conf` (28 KB): **PRUNE unless Miyoko actively uses
  sign-in automation.** It installs ~100 `script-request-header/body`
  cookie-capture rewrites for JD, Tieba, Bilibili, iQIYI, 10086/10010/189,
  Meituan, WPS, etc., calling scripts from NobyDa/chavyleung repos.
  Highest credential-exposure surface in the mirror.
  > 中文：`JS_GetCookie.conf`（28 KB）：**精简，除非 Miyoko 积极使用签到自动化。**它安装了约 100 条 `script-request-header/body` 类型的 cookie 捕获重写，针对京东、贴吧、哔哩哔哩、爱奇艺、10086/10010/189、美团、WPS 等，调用来自 NobyDa/chavyleung 仓库的脚本。是镜像中凭证暴露面最大的部分。
- `JD_TB_price.conf`: **PRUNE unless used** — price-history rewrite that
  calls `https://service.2ti.st/…/main.js` (third-party host, not GitHub).
  > 中文：`JD_TB_price.conf`：**精简，除非使用**——价格历史重写，调用 `https://service.2ti.st/…/main.js`（第三方托管，非 GitHub）。
- `YouTube.conf`, `TikTok.conf`, `Netflix_ratings.conf`:
  **PRUNE unless used.** Small single-purpose rewrites (YouTube ad
  redirect, TikTok `CN → JP` region, Netflix ratings via yichahucha's
  script). `TikTok.conf` overlaps `kop-xiao/TikTok_Rewrite.list` — keep
  at most one.
  > 中文：`YouTube.conf`、`TikTok.conf`、`Netflix_ratings.conf`：**精简，除非使用。**小型单用途重写（YouTube 广告重定向、TikTok `CN → JP` 地区、通过 yichahucha 的脚本实现的 Netflix 评分）。`TikTok.conf` 与 `kop-xiao/TikTok_Rewrite.list` 重叠——最多保留一个。
- `Bili_Auto_Regions.conf` + `.js`: see §1.3 — keep only if Bilibili
  HK/TW is used.
  > 中文：`Bili_Auto_Regions.conf` + `.js`：见 §1.3——仅当使用 Bilibili 港台番剧时保留。

### 2.4 Repo hygiene (applies to all three folders)
> 中文：2.4 仓库卫生（适用于全部三个文件夹）
- Add a root `.gitignore` (`*.DS_Store`, `*.p12`, `*.mobileconfig`,
  local overlay files — see §5). None of the three clones ships one.
  > 中文：添加根目录 `.gitignore`（`*.DS_Store`、`*.p12`、`*.mobileconfig`、本地覆盖文件——见 §5）。三个克隆中都没有自带该文件。
- Do **not** commit the `.git/` directories themselves (they are already
  excluded by the mirror layout — keep it that way).
  > 中文：不要提交 `.git/` 目录本身（镜像布局中已将其排除——保持这样）。
- Before publishing, verify each upstream's licence. No `LICENSE` file
  was checked in this analysis — confirm Orz-3, KOP-XIAO and ConnersHua
  permit redistribution, and keep attribution + upstream links in the
  README.
  > 中文：发布前核实每个上游的许可证。本分析未检查 `LICENSE` 文件——确认 Orz-3、KOP-XIAO 和 ConnersHua 允许再分发，并在 README 中保留署名和上游链接。

## 3. What to add / supplement
> 中文：3. 需要补充 / 添加的内容

| Add<br>中文：添加项 | Why<br>中文：原因 | Priority<br>中文：优先级 |
|---|---|---|
| `rulego/Surge/Ruleset/Extra/Reject/Malicious.list` (100 rules, `force-policy=reject`)<br>中文：（100 条规则，`force-policy=reject`） | Config has Advertising + Tracking but **no** malicious-domain blocking. Cheap (100 lines), same pattern as the two Reject lists already loaded. Note it contains some aggressive entries (`polyfill.io`, `bootcdn.net`, `staticfile.org`) — expect occasional false positives on dev sites.<br>中文：配置已有 Advertising 和 Tracking，但**没有**恶意域名拦截。成本低（100 行），与已加载的两个 Reject 列表模式相同。注意其中有一些较激进的条目（`polyfill.io`、`bootcdn.net`、`staticfile.org`）——在开发网站上偶尔可能出现误报。 | High<br>中文：高 |
| `rulego/Surge/Ruleset/Extra/Scholar.list` (66 rules: ACM, ACS, Nature/Cell/Science publishers, Cambridge, etc.)<br>中文：（66 条规则：ACM、ACS、Nature/Cell/Science 等出版商、Cambridge 等） | Miyoko is a Harvard graduate student; scholarly publishers currently fall through to `Proxy.list`/`final`. Give them an explicit policy (e.g. 美国节点 or 全球加速) so paywalled/library resources route predictably.<br>中文：Miyoko 是哈佛研究生；学术出版商目前会落到 `Proxy.list`/`final`。给它们一个明确的策略（例如 美国节点或 全球加速），使付费/图书馆资源路由可预测。 | High<br>中文：高 |
| `Orz-3/mini` icons as `orz-3-mini/` (or a pinned copy of `Color/*.png` + `none/qikuo.png`)<br>中文：`Orz-3/mini` 图标，保存为 `orz-3-mini/`（或固定复制 `Color/*.png` + `none/qikuo.png`） | **Gap:** the config references 13 icon URLs from `Orz-3/mini` (`Color/Global, Apple, Streaming, StreamingSE, Telegram, Final, Static, HK, TW, JP, SG, US.png` + `none/qikuo.png` profile image) and **none** of them is in the mirror. Without this folder the curated config's icons 404. Needs approval — it is a fourth upstream repo; check its size/licence first. Alternative: re-point `img-url`s at `kop-xiao/img/policy/*` and prune mini entirely.<br>中文：**缺口：**配置引用了来自 `Orz-3/mini` 的 13 个图标 URL（`Color/Global, Apple, Streaming, StreamingSE, Telegram, Final, Static, HK, TW, JP, SG, US.png` + `none/qikuo.png` 个人资料图片），而**其中没有一个**在镜像中。没有这个文件夹，精选配置的图标会 404。需要批准——这是第四个上游仓库；先检查其大小/许可证。替代方案：将 `img-url` 改指 `kop-xiao/img/policy/*`，并完全删除 mini。 | High<br>中文：高 |
| `README.md` (repo root)<br>中文：（仓库根目录） | Attribution, upstream commit pins, update procedure (`git fetch` per folder, diff review before push), and the local-vs-public split (§5).<br>中文：署名、上游提交固定版本、更新流程（每个文件夹 `git fetch`，推送前检查差异），以及本地与公开的区分（§5）。 | High<br>中文：高 |
| Root `.gitignore`<br>中文：根目录 `.gitignore` | `*.DS_Store`, `*.p12`, `*.mobileconfig`, `local/` (see §5). | High<br>中文：高 |
| `rulego/Surge/Ruleset/Extra/GenAI/Meta.list` — **only as a reference**<br>中文：——**仅作为参考** | Not needed as a list (subset of `AI.list`), but it documents the Meta/Muse domains. The local Muse rules (§5) can be trimmed against it.<br>中文：作为列表不需要（是 `AI.list` 的子集），但它记录了 Meta/Muse 的域名。本地 Muse 规则（§5）可据此精简。 | Low<br>中文：低 |
| Optional, off by default: `Extra/X.list`, `Extra/GeoLoc.list`, `Extra/Crypto.list`, `Extra/PayPal.list`, `Surge/Module/Block/iOSUpdate.sgmodule`<br>中文：可选，默认关闭：`Extra/X.list`、`Extra/GeoLoc.list`、`Extra/Crypto.list`、`Extra/PayPal.list`、`Surge/Module/Block/iOSUpdate.sgmodule` | Candidates if Miyoko wants X/Twitter, geo-location services, crypto, PayPal, or iOS-OTA blocking split out. List them in the README as opt-in, do not load by default.<br>中文：如果 Miyoko 希望将 X/Twitter、地理位置服务、加密货币、PayPal 或 iOS OTA 拦截单独拆分，可作为候选。在 README 中列为可选加入，默认不加载。 | Low<br>中文：低 |
| `Quantumult/Sample.conf` filter-order note<br>中文：`Quantumult/Sample.conf` 过滤顺序说明 | Already kept as reference (§1.1) — no new file needed.<br>中文：已作为参考保留（§1.1）——无需新文件。 | — |

## 4. Bugs / stale links found (fix regardless of pruning)
> 中文：4. 发现的错误 / 失效链接（无论是否删减都要修复）

1. **`geo_location_checker` URL is stale.** `Orz-3.conf:8` points to
   `https://raw.githubusercontent.com/Orz-3/Orz-3/master/QuantumultX/IP.js`
   (repo `Orz-3`, folder `QuantumultX`), but the cloned repo is
   `Orz-3/QuantumultX` with `IP.js` at its root — the correct raw URL
   would be `…/Orz-3/QuantumultX/master/IP.js`. Verify live; if 404,
   switch to RuleGo's `Quantumult/Script/geo_location_checker.js`
   (what `Sample.conf` uses) or KOP-XIAO's `Scripts/IP_API.js`.
  > 中文：**`geo_location_checker` 的 URL 已失效。**`Orz-3.conf:8` 指向 `https://raw.githubusercontent.com/Orz-3/Orz-3/master/QuantumultX/IP.js`（仓库 `Orz-3`，文件夹 `QuantumultX`），但克隆的仓库是 `Orz-3/QuantumultX`，`IP.js` 在其根目录——正确的 raw URL 应为 `…/Orz-3/QuantumultX/master/IP.js`。请在线验证；若返回 404，改用 RuleGo 的 `Quantumult/Script/geo_location_checker.js`（`Sample.conf` 所用的）或 KOP-XIAO 的 `Scripts/IP_API.js`。
2. **`streaming-ui-check.js` has no policy to report to.**
   Line 35 hard-codes `policy_name = "Netflix"`, but the config defines
   no `Netflix` policy group. Either add a Netflix/流媒体 policy group
   or edit the script's `policy_name` to an existing group
   (e.g. `国际媒体`). Also note the script contacts Netflix, YouTube,
   Disney+, DAZN, Paramount+, Discovery+ and ChatGPT on every run.
  > 中文：**`streaming-ui-check.js` 没有可汇报的策略。**第 35 行硬编码了 `policy_name = "Netflix"`，但配置中没有定义 `Netflix` 策略组。要么添加一个 Netflix/流媒体策略组，要么将脚本的 `policy_name` 改为已有组（例如 `国际媒体`）。另请注意，该脚本每次运行时都会联系 Netflix、YouTube、Disney+、DAZN、Paramount+、Discovery+ 和 ChatGPT。
3. **RuleGo default-branch trap.** The default branch is a lookalike
   `maѕter` (Cyrillic `ѕ`) containing only a README; the real content is
   on branch `master`. Any future `git clone` / fetch must specify
   `master` explicitly, and the README must warn contributors.
  > 中文：**RuleGo 默认分支陷阱。**默认分支是一个形似的 `maѕter`（西里尔字母 `ѕ`），其中只有一个 README；真正的内容在 `master` 分支上。任何未来的 `git clone` / fetch 都必须明确指定 `master`，README 也必须提醒贡献者。
4. **`Task/*.json` mostly invalid JSON** (§2.3) — 20/22 fail strict
   parsing (trailing commas). Do not import blindly.
  > 中文：**`Task/*.json` 大多是无效 JSON**（§2.3）——20/22 未通过严格解析（尾随逗号）。不要盲目导入。
5. **Ordering is load-bearing, not cosmetic.** `TikTok.list` is loaded
   *before* `!CN.list` so TikTok → 美国节点 while other streaming →
   国际媒体, even though TikTok's domains are all inside `!CN.list`.
   Same for `Direct+` first / `Proxy+` last. Document the order in the
   curated config; do not alphabetise it.
  > 中文：**顺序是起作用的，不是装饰。**`TikTok.list` 在 `!CN.list` **之前**加载，因此 TikTok → 美国节点，而其他流媒体 → 国际媒体，尽管 TikTok 的域名全部在 `!CN.list` 内。`Direct+` 在前 / `Proxy+` 在后也是如此。在精选配置中记录该顺序；不要按字母顺序排列。

## 5. What must stay LOCAL (never in the public mirror)
> 中文：5. 必须保留在本地的内容（绝不要放进公开镜像）

- **Miyoko's 13 custom `filter_local` rules** (`muse.ai`, `meta.ai`,
  `meta.com`, `facebook.com`, `fbcdn.net`, `metaaiusercontent.com`,
  `atmeta.com`, `metaaivm.com`, `graph.facebook.com`, `graph.meta.ai`,
  `shortwave.facebook.com`, `hatch-api.meta.ai`, `api.muse.ai` → 美国节点).
  They must live in a **local-only overlay**, e.g. a gitignored
  `local/muse-filter-local.conf` (or a private repo) that is appended to
  the curated config on her device — never committed to
  `miyoko0204/quantumultx-mirror`.
  > 中文：**Miyoko 的 13 条自定义 `filter_local` 规则**（`muse.ai`、`meta.ai`、`meta.com`、`facebook.com`、`fbcdn.net`、`metaaiusercontent.com`、`atmeta.com`、`metaaivm.com`、`graph.facebook.com`、`graph.meta.ai`、`shortwave.facebook.com`、`hatch-api.meta.ai`、`api.muse.ai` → 美国节点）。它们必须放在**仅本地的覆盖层**中，例如被 git 忽略的 `local/muse-filter-local.conf`（或私有仓库），在其设备上追加到精选配置中——绝不要提交到 `miyoko0204/quantumultx-mirror`。
  - Pruning note: 6 of the 13 are already covered by `AI.list`
    (`muse.ai`, `meta.ai`, `meta.com`, `facebook.com`, `fbcdn.net`,
    `graph.facebook.com`) and 3 more by `Proxy.list`; `api.muse.ai` is a
    subdomain of `muse.ai`. The genuinely additive local rules are
    `metaaivm.com`, `metaaiusercontent.com`, `atmeta.com`,
    `graph.meta.ai`, `shortwave.facebook.com`, `hatch-api.meta.ai`.
    Keep the full 13 locally (explicit beats implicit), but the overlay
    can be trimmed to those 6 if she wants it minimal.
  > 中文：精简说明：13 条中有 6 条已被 `AI.list` 覆盖（`muse.ai`、`meta.ai`、`meta.com`、`facebook.com`、`fbcdn.net`、`graph.facebook.com`），另有 3 条被 `Proxy.list` 覆盖；`api.muse.ai` 是 `muse.ai` 的子域名。真正新增的本地规则是 `metaaivm.com`、`metaaiusercontent.com`、`atmeta.com`、`graph.meta.ai`、`shortwave.facebook.com`、`hatch-api.meta.ai`。本地保留完整的 13 条（明确优于隐含），但如果她想要最简版本，覆盖层可精简为这 6 条。
- **MITM certificate material.** The local config's `[mitm]` section
  contains a passphrase and a base64 `p12` blob. **These values must
  never appear in the repo, in `ANALYSIS.md`, in commit messages, or in
  screenshots.** The public curated config should ship `[mitm]` with
  `hostname = www.google.cn` only (matching upstream `Orz-3.conf`) and
  instructions to generate/install her own certificate on-device.
  `skip_validating_cert = true` is already in upstream `Orz-3.conf`;
  keep it as-is and do not broaden `hostname`.
  > 中文：**MITM 证书材料。**本地配置的 `[mitm]` 部分包含口令和 base64 编码的 `p12` 文件。**这些值绝不能出现在仓库、`ANALYSIS.md`、提交信息或截图中。**公开的精选配置应只在 `[mitm]` 中提供 `hostname = www.google.cn`（与上游 `Orz-3.conf` 一致），并说明在设备上自行生成/安装证书。`skip_validating_cert = true` 已在上游 `Orz-3.conf` 中；保持原样，不要扩大 `hostname`。
- **Node subscriptions / server lists.** `[server_remote]` is empty in
  both upstream and local configs — keep it that way in the public repo.
  No proxy credentials, tokens or subscription URLs belong in the mirror.
  > 中文：**节点订阅 / 服务器列表。**上游和本地配置中的 `[server_remote]` 均为空——公开仓库中保持这样。镜像中不应有任何代理凭据、令牌或订阅 URL。

## 6. Security notes
> 中文：6. 安全注意事项

- **`resource-parser.js` is the highest-trust file in the mirror.**
  266 KB / 5,740 lines, runs on *every* remote-resource parse
  (`opt-parser=true` on all 14 filters + 4 rewrites), parses node
  subscriptions including passwords, and embeds `shrtm.nu` short links
  and `t.me` channel links in its notification payloads. It is required,
  but: pin it to a reviewed commit (the config currently loads `@master`
  via jsDelivr — floating), and diff it on every update before pushing.
  No live credentials were found in it — the `password=` strings in the
  grep hits are parser code and example comments, not real secrets.
  > 中文：**`resource-parser.js` 是镜像中信任度要求最高的文件。**266 KB / 5,740 行，在每次远程资源解析时运行（全部 14 个过滤器 + 4 个重写都设置了 `opt-parser=true`），解析包含密码的节点订阅，并在通知内容中嵌入 `shrtm.nu` 短链接和 `t.me` 频道链接。它是必需的，但：应将其固定到已审查的提交（配置目前通过 jsDelivr 加载 `@master`——浮动版本），并在每次更新前检查差异。其中未发现真实凭据——grep 命中的 `password=` 字符串是解析器代码和示例注释，不是真实密钥。
- **Scripts that execute code / phone home.** `streaming-ui-check.js`
  and its 9 sibling check scripts make outbound requests to streaming
  services; `IP*.js` / `geo_location*.js` send the node IP to
  `ip-api.com`. `server-info-plus.js`, `traffic-check.js` query node
  traffic APIs. None of these is loaded by the local config except
  `streaming-ui-check.js` — keep it that way.
  > 中文：**执行代码 / 对外联网的脚本。**`streaming-ui-check.js` 及其 9 个同类检查脚本会向流媒体服务发出出站请求；`IP*.js` / `geo_location*.js` 会将节点 IP 发送到 `ip-api.com`。`server-info-plus.js`、`traffic-check.js` 会查询节点流量 API。本地配置加载的只有 `streaming-ui-check.js`——保持这样。
- **MITM / rewrite modules.** The 4 `rewrite_remote` entries are the
  main MITM surface. Only 1 of 4 (`RuleGo/Advertising.sgmodule`) is in
  the mirror. The other 3 are **not mirrored and not reviewed here**:
  `Aioneas/Surge` YouTube hide-shorts, `app2smile/rules` Tieba, and
  `https://ddgksf2013.top/rewrite/StartUpAds.conf` — the last is a
  non-GitHub domain and the least trustworthy of the three. Options:
  (a) mirror Aioneas + app2smile as `third-party/` with provenance
  warnings, (b) drop `ddgksf2013.top` and replace its splash-ad blocking
  with the RuleGo Advertising module already loaded, or (c) leave all
  three as remote-only and document them as unreviewed. **Do not**
  silently vendor them.
  > 中文：**MITM / 重写模块。**4 个 `rewrite_remote` 条目是主要的 MITM 攻击面。其中只有 1 个（`RuleGo/Advertising.sgmodule`）在镜像中。另外 3 个**未镜像、也未在此审查**：`Aioneas/Surge` 的 YouTube 隐藏 Shorts、`app2smile/rules` 的 Tieba，以及 `https://ddgksf2013.top/rewrite/StartUpAds.conf`——最后一个是非 GitHub 域名，是三者中最不可信的。选项：(a) 将 Aioneas + app2smile 作为 `third-party/` 镜像并附来源警告，(b) 删除 `ddgksf2013.top`，用已加载的 RuleGo Advertising 模块替代其开屏广告拦截，或 (c) 将三者都保留为仅远程并记录为未审查。**不要**悄悄将它们收录进来。
- **`JS_GetCookie.conf` + `Task/*.json`** (see §2.3): cookie-capture
  rewrites and daily scripts that read QX-stored cookies for JD/Weibo/
  carriers/etc. If Miyoko uses any of them, she should know the scripts
  come from third-party repos (NobyDa, chavyleung, …) and can exfiltrate
  those cookies. No tokens are embedded in the mirrored JSON files
  themselves (checked by grep for `cookie|token|auth|password|session`).
  > 中文：**`JS_GetCookie.conf` + `Task/*.json`**（见 §2.3）：Cookie 捕获重写和每日脚本，会读取 QX 中存储的京东/微博/运营商等 Cookie。如果 Miyoko 使用其中任何一个，她应知道这些脚本来自第三方仓库（NobyDa、chavyleung 等），可能外泄这些 Cookie。镜像的 JSON 文件本身未嵌入令牌（已用 grep 检查 `cookie|token|auth|password|session`）。
- **`.DS_Store` × 5** in `kop-xiao/` — delete; add to `.gitignore`.
  > 中文：**`kop-xiao/` 中的 `.DS_Store` × 5**——删除；加入 `.gitignore`。
- **No secrets were found in the three clones** (grep for
  `passphrase|p12|api-key|secret|password` excluding `.git/` returns only
  parser code). The only secret material in scope is in the *local*
  config (§5) — which is why §5 exists.
  > 中文：**三个克隆中未发现密钥**（grep 搜索 `passphrase|p12|api-key|secret|password`，排除 `.git/` 后仅返回解析器代码）。范围内唯一的密钥材料在*本地*配置中（§5）——这正是 §5 存在的原因。

## 7. Proposed curated Quantumult X config outline
> 中文：7. 建议的精选 Quantumult X 配置大纲

A single public file, e.g. `curated/Miyoko.conf` (or repo-root
`QuantumultX.conf`), built from the mirror with **no local secrets**:
> 中文：一个公开文件，例如 `curated/Miyoko.conf`（或仓库根目录的 `QuantumultX.conf`），由镜像构建，**不含本地密钥**：

```
[general]
  resource_parser_url = <pinned jsDelivr commit of kop-xiao resource-parser.js>
  network_check_url / server_check_url — as Orz-3.conf
  geo_location_checker = http://ip-api.com/json/?lang=zh-CN,
      <rulego Quantumult/Script/geo_location_checker.js, fixed URL>
  profile_img_url = <orz-3-mini qikuo.png, once mirrored — or drop>
[dns]            — as Orz-3.conf (119.29.29.29 / 180.184.1.1 / 223.5.5.5)
[policy]         — as Orz-3.conf (11 groups; img-urls → orz-3-mini/ or kop-xiao/img/policy/)
[server_remote]  — empty (user adds their own subscription privately)
[filter_remote]  — in this exact order (first-match-wins):
   1. Direct+.list            → direct
   2. Reject/Advertising.list → reject
   3. Reject/Tracking.list    → reject
   4. Reject/Malicious.list   → reject          # NEW (§3)
   5. AI.list                  → 美国节点
   6. Scholar.list             → 美国节点        # NEW (§3)
   7. Streaming/Video/TikTok.list → 美国节点    # before !CN — ordering trick (§4.5)
   8. Google/GoogleVoice.list  → 美国节点
   9. Telegram.list            → 电报代理
  10. Streaming/!CN.list       → 国际媒体
  11. Streaming/CN.list        → 港台番剧
  12. Proxy.list               → 全球加速
  13. Apple/Apple.list         → 苹果服务
  14. Direct.list              → direct
  15. WeChat.list              → direct
  16. Proxy+.list              → proxy
  (all update-interval=172800, opt-parser=true; consider
   inserted-resource=true as in RuleGo Sample.conf for the Reject lists)
[rewrite_remote]
  1. RuleGo Surge/Module/Block/Advertising.sgmodule   # in-mirror
  2-4. Aioneas / app2smile / ddgksf2013 — see §6 decision (default: leave
      remote-only + documented, or drop ddgksf2013.top)
[filter_local]   — LAN + geoip cn direct + final 黑白名单 (as Orz-3.conf)
                 + LOCAL OVERLAY appended on-device only (§5, not in repo)
[rewrite_local]  — google.cn → google.com 302 (as Orz-3.conf)
[task_local]     — streaming-ui-check.js (fix policy_name, §4.2)
[mitm]           — hostname = www.google.cn; skip_validating_cert = true;
                 NO passphrase / p12 in the repo — user installs own cert
```
> 中文：这是一份公开的精选配置大纲，不含任何本地密钥，按所示的确切章节顺序排列：先是通用设置、DNS、策略组和空的服务器远程列表，然后是按首次匹配优先顺序排列的远程过滤器（包括新增的恶意域名拦截和学术列表，以及 TikTok 在 !CN 之前的顺序技巧），接着是远程重写、本地过滤器、本地重写、本地任务和 MITM 设置，其中 MITM 仅含主机名而不含证书口令或 p12 文件。

Local overlay (gitignored, never pushed): `local/` containing the 13
Muse/Meta rules and the MITM `passphrase`/`p12`, applied on-device after
importing the curated config.
> 中文：本地覆盖层（被 git 忽略，永不推送）：`local/`，包含 13 条 Muse/Meta 规则和 MITM 的 `passphrase`/`p12`，在导入精选配置后于设备上应用。

## 8. Suggested execution order (after approval)
> 中文：8. 建议的执行顺序（获得批准后）

1. Delete `.DS_Store` × 5; add root `.gitignore` + `README.md`.
  > 中文：删除 `.DS_Store` × 5；添加根目录 `.gitignore` + `README.md`。
2. Prune §2.1–2.3 (GenAI, per-service Streaming/Apple lists, Shadow,
   Surge profile, dragonball/policy/southpark icons, unused KOP scripts,
   Task gallery, JS_GetCookie/JD_TB_price) — or move to `archive/` if
   Miyoko prefers reversible pruning.
  > 中文：删减 §2.1–2.3（GenAI、按服务的流媒体/Apple 列表、Shadow、Surge 配置、dragonball/policy/southpark 图标、未使用的 KOP 脚本、Task 库、JS_GetCookie/JD_TB_price）——如果 Miyoko 更喜欢可逆的删减，也可以移到 `archive/`。
3. Add `Reject/Malicious.list` + `Scholar.list` to `filter_remote`;
   mirror `Orz-3/mini` icons (or re-point `img-url`s).
  > 中文：将 `Reject/Malicious.list` + `Scholar.list` 添加到 `filter_remote`；镜像 `Orz-3/mini` 图标（或重新指向 `img-url`）。
4. Fix the two stale items (§4.1 geo-location URL, §4.2 `policy_name`).
  > 中文：修复两个失效项（§4.1 地理位置 URL，§4.2 `policy_name`）。
5. Decide the 3 non-mirrored `rewrite_remote` entries (§6).
  > 中文：决定 3 个未镜像的 `rewrite_remote` 条目（§6）。
6. Build `curated/Miyoko.conf` per §7; verify it imports cleanly in
   Quantumult X **before** any push; keep the local overlay separate.
  > 中文：按 §7 构建 `curated/Miyoko.conf`；在任何推送**之前**验证它能在 Quantumult X 中干净导入；本地覆盖层保持分离。
7. Verify upstream licences, then push.
  > 中文：核实上游许可证，然后推送。
