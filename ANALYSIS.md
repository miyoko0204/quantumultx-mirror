# Quantumult X Mirror — Analysis & Pruning Plan

> Repo: `miyoko0204/quantumultx-mirror`
> Upstreams (cloned 2026-11-17, all on branch `master`):
> - `orz-3/` ← Orz-3/QuantumultX @ `a014cd9` — 32 files, ~98 KB
> - `kop-xiao/` ← KOP-XIAO/QuantumultX @ `cce2996` — 100 files, ~1.1 MB
> - `rulego/` ← ConnersHua/RuleGo @ `a7e5c4b` — 130 files, ~131 KB
> Local reference config: `quantumult_20261117220356.conf`
>   (= `orz-3/Orz-3.conf` + 13 local Muse/Meta rules + local MITM cert — see §5)
> Status: **DRAFT — nothing in this plan has been executed. Every removal
> below needs Miyoko's approval.**

## 0. How the local config actually uses the mirror

Only a small fraction of the mirrored files is referenced:

| Used by local config | Source in mirror |
|---|---|
| `resource-parser.js` (`[general]`, via jsDelivr) | `kop-xiao/Scripts/resource-parser.js` |
| `streaming-ui-check.js` (`[task_local]`) | `kop-xiao/Scripts/streaming-ui-check.js` |
| `IP.js` (`geo_location_checker`, stale URL — see §4) | `orz-3/IP.js` |
| 14 × `filter_remote` | `rulego/Surge/Ruleset/…` (Direct, Direct+, Proxy, Proxy+, AI, Apple/Apple, Telegram, WeChat, GoogleVoice, TikTok, Streaming `!CN` + `CN`, Reject Advertising + Tracking) |
| 1 of 4 × `rewrite_remote` | `rulego/Surge/Module/Block/Advertising.sgmodule` |
| 13 policy icons + profile image | `Orz-3/mini` — **NOT mirrored yet** (§4) |

Everything else in the three folders is currently unused ballast or
optional material. The rest of this file sorts it into keep / prune / add.

## 1. What to keep

### 1.1 rulego/ — keep the 14 lists the config loads, plus references
- `Surge/Ruleset/Direct.list` (32 lines) and `Direct+.list` (28 lines):
  **complementary, zero domain overlap.** `Direct+` is corrections
  (Google `dl/update.googleapis.com`, Umeng, Strava analytics, Tencent live).
  Keep both, keep the order (Direct+ first — matches RuleGo's own
  `Quantumult/Sample.conf`).
- `Surge/Ruleset/Proxy.list` (565 lines) and `Proxy+.list` (2 lines):
  keep both. `Proxy+` is a single rule (`GEOIP,GOOGLE`); it is trivial but
  harmless, and it sits last in both the local config and upstream's sample.
  Do **not** reorder without testing — Quantumult X is first-match-wins.
- `Surge/Ruleset/Extra/AI.list` (117 lines, 75 domains): keep as the single
  AI list. It already covers `muse.ai`, `meta.ai`, `meta.com`,
  `facebook.com`, `fbcdn.net`, `graph.facebook.com` (see §5).
- `Surge/Ruleset/Extra/Apple/Apple.list` (20 lines): keep as the single
  Apple list. Its `DOMAIN-SUFFIX` umbrella (`apple.com`, `icloud.com`,
  `mzstatic.com`, `apple.com.cn` …) already covers most Apple subdomains.
- `Surge/Ruleset/Extra/Reject/Advertising.list` (1,077 lines) and
  `Tracking.list` (109 lines): keep both. Overlap is only 3 domains
  (`growingio.com`, `quantserve.com`, `socdm.com`) — they are complementary,
  not duplicates.
- `Surge/Ruleset/Extra/Streaming/!CN.list` (303 lines) and `CN.list`
  (18 lines): keep both, zero overlap with each other.
- `Surge/Ruleset/Extra/Telegram.list`, `WeChat.list`,
  `Google/GoogleVoice.list`, `Streaming/Video/TikTok.list`: keep — each is
  small and directly referenced by the config.
- `Surge/Module/Block/Advertising.sgmodule`: keep — it is one of the 4
  `rewrite_remote` entries. Note it is Surge-format; Quantumult X parses it
  via `resource-parser.js`.
- `Quantumult/Sample.conf` (206 lines) + `Quantumult/Script/geo_location_checker.js`:
  keep **as reference only**. Sample.conf is the cleanest curated-config
  template available (same filter order as the local config) and its
  geo-location script is the maintained replacement for the broken Orz-3
  IP.js URL (§4). Do not activate Sample.conf as-is.

### 1.2 kop-xiao/ — keep only what the config needs
- `Scripts/resource-parser.js` (266 KB, 5,740 lines): **required** — every
  `filter_remote`/`rewrite_remote` uses `opt-parser=true`. Keep, but see
  security note (§6): pin it, review diffs on update.
- `Scripts/streaming-ui-check.js` (614 lines): keep — the only `[task_local]`
  entry. Fix its `policy_name = "Netflix"` first: the local config has **no**
  `Netflix` policy group, so the check currently has nowhere to report to
  (§4).
- `img/southpark/7.png`: keep — it is the `media-url` thumbnail hard-coded
  in `streaming-ui-check.js` and 8 sibling check scripts. (Note: PNGs are
  not in the mirror yet — the connector snapshot push accepts text only.
  See README.md.)
- `README.md`, `QuantumultX_Profiles.conf`: keep as reference (commented
  template; not loaded by the config).

### 1.3 orz-3/ — keep the base config and the two scripts it points to
- `Orz-3.conf`: keep — it **is** the local config minus local additions.
- `IP.js` (12 lines, parses ip-api.com JSON): keep, but its URL in the
  config is stale (§4).
- `Bili_Auto_Regions.js` + `Bili_Auto_Regions.conf`: keep **only if**
  Miyoko watches Bilibili HK/TW番剧; otherwise prune with the other
  media confs below.

## 2. What to remove / prune (approval needed for each)

### 2.1 rulego/ — whole subtrees the config never loads
- `Surge/Ruleset/Extra/GenAI/` (13 files, 64 domains combined):
  **PRUNE as separate lists.** Measured overlap: all 64 GenAI domains are
  already inside `AI.list` (64/64). `AI.list` only adds 11 more
  (Apple Intelligence relay hosts, `openrouter.ai`, `zed.dev`). Keeping
  GenAI/* would only be useful if Miyoko wants a *per-vendor* AI policy
  (e.g. Anthropic → US node, Meta → US node separately) — she does not
  today. Delete, or move to `archive/` if she wants the split later.
- `Surge/Ruleset/Extra/Streaming/Video/` (38 files), `Music/` (9),
  `Podcast/` (2), `Live/Twitch.list`: **PRUNE.** `!CN.list` is a superset
  of 37 of the 38 Video lists (100% of each list's domains; only
  `Youku.list` 0/13 and `Prime-Video.list` 14/15 are exceptions) and of
  Music 15/18, Podcast 2/2, Live 6/6. Consequence: `TikTok.list`
  (10/10 domains already in `!CN.list`) is redundant as a *list*, but the
  config loads it **before** `!CN.list` so TikTok still routes to 美国节点
  while other streaming goes to 国际媒体 — that ordering trick is the only
  reason to keep `TikTok.list` itself. The other ~48 files add nothing.
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
- `Surge/Ruleset/Extra/Google/` (except `GoogleVoice.list`),
  `Extra/Microsoft/`, `Extra/PayPal.list`, `Extra/X.list`,
  `Extra/Crypto.list` (49 rules), `Extra/GeoLoc.list` (21 rules):
  **PRUNE by default.** Zero overlap with the lists the config uses
  (e.g. `GoogleDrive.list` shares 0 domains with `Google.list`), so they
  are not duplicates — they are just unused scope. `Scholar.list` is the
  one exception to consider adding (§3).
- `Surge/Profile.conf`, `Surge/README.md`: **PRUNE** — Surge app config,
  not Quantumult X.
- `Shadow/` (`Profile.conf`, `README.md`): **PRUNE** — Shadowrocket
  config, not Quantumult X.
- `Surge/Module/` except `Block/Advertising.sgmodule` (i.e. `Block/`
  `AwemeAds`, `YouTubeAds`, `KindleUpdate`, `iOSUpdate`; `Unlock/`
  Netflix, bilibili ×2, AbeamTV; `Fix/` AirPlay, DDPai, Imaging-Edge;
  `FCM`, `GeoLoc`, `Hide-VPN-Icon`): **PRUNE by default.** They are
  Surge modules for unlocking/ad-blocking specific apps. Only re-add one
  if Miyoko asks for that app (e.g. `Unlock/Netflix.sgmodule` if she
  wants Netflix unlocking, `Block/iOSUpdate.sgmodule` to block OTA).
- `Surge/Script/bstar.js`: **PRUNE** — single Surge script, unused.

### 2.2 kop-xiao/ — the bulk is icons and unused check scripts
- `img/dragonball/` (34 files, ~428 KB): **PRUNE.** Zero references in any
  script, config or JSON in the repo. Pure decoration.
- `img/policy/` (17 files, ~120 KB): **PRUNE unless** Miyoko wants to
  switch policy icons from Orz-3/mini to these. The local config uses
  Orz-3/mini `Color/*.png`, never these.
- `img/southpark/` except `7.png` (9 files, ~130 KB): **PRUNE.** Only
  `7.png` is referenced (by 9 check scripts). Keep `7.png`, drop 1–6, 8–10.
- `img/sub/Dler.png`, `img/others/lee.png`: **PRUNE** — unreferenced.
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
- `Scripts/TaskGallery.json` (HotKids gallery, ~11 KB), `Scripts/UI-Action.json`,
  `Scripts/emoji.json`, `Scripts/Cron-Switch.json`: **PRUNE by default**
  (reference material; TaskGallery points at third-party repos incl. a
  Gitee host — see §6).
- `HotKids-JS/scripts.conf`: **PRUNE** — empty file.
- `Q-Search.list`, `TikTok_Rewrite.list`: **PRUNE by default** — Safari
  search-shortcut rewrites and a TikTok region rewrite (`CN → JP`,
  version pin `8.4.0`) that duplicate `orz-3/TikTok.conf`. Keep only if
  Miyoko uses them; note the TikTok version pin is years out of date.
- `QuanX-RuleSet-Divine-Engine.json`, `remote-resource.json`:
  **PRUNE.** Both point at dead/stale upstreams
  (`ConnersHua/Profiles`, `cloudcompute.lbyczf.com` proxy rewrites,
  `gist/.../Fndroid`) and at `crossutility/Quantumult-X` sample servers.
  Do not load either.
- `.DS_Store` × 5 (`kop-xiao/`, `img/`, `img/southpark/`, `Scripts/`,
  `HotKids-JS/`): **DELETE.** macOS Finder junk, no function.

### 2.3 orz-3/ — the Task gallery and cookie-capture confs
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
- `JS_GetCookie.conf` (28 KB): **PRUNE unless Miyoko actively uses
  sign-in automation.** It installs ~100 `script-request-header/body`
  cookie-capture rewrites for JD, Tieba, Bilibili, iQIYI, 10086/10010/189,
  Meituan, WPS, etc., calling scripts from NobyDa/chavyleung repos.
  Highest credential-exposure surface in the mirror.
- `JD_TB_price.conf`: **PRUNE unless used** — price-history rewrite that
  calls `https://service.2ti.st/…/main.js` (third-party host, not GitHub).
- `YouTube.conf`, `TikTok.conf`, `Netflix_ratings.conf`:
  **PRUNE unless used.** Small single-purpose rewrites (YouTube ad
  redirect, TikTok `CN → JP` region, Netflix ratings via yichahucha's
  script). `TikTok.conf` overlaps `kop-xiao/TikTok_Rewrite.list` — keep
  at most one.
- `Bili_Auto_Regions.conf` + `.js`: see §1.3 — keep only if Bilibili
  HK/TW is used.

### 2.4 Repo hygiene (applies to all three folders)
- Add a root `.gitignore` (`*.DS_Store`, `*.p12`, `*.mobileconfig`,
  local overlay files — see §5). None of the three clones ships one.
- Do **not** commit the `.git/` directories themselves (they are already
  excluded by the mirror layout — keep it that way).
- Before publishing, verify each upstream's licence. No `LICENSE` file
  was checked in this analysis — confirm Orz-3, KOP-XIAO and ConnersHua
  permit redistribution, and keep attribution + upstream links in the
  README.

## 3. What to add / supplement

| Add | Why | Priority |
|---|---|---|
| `rulego/Surge/Ruleset/Extra/Reject/Malicious.list` (100 rules, `force-policy=reject`) | Config has Advertising + Tracking but **no** malicious-domain blocking. Cheap (100 lines), same pattern as the two Reject lists already loaded. Note it contains some aggressive entries (`polyfill.io`, `bootcdn.net`, `staticfile.org`) — expect occasional false positives on dev sites. | High |
| `rulego/Surge/Ruleset/Extra/Scholar.list` (66 rules: ACM, ACS, Nature/Cell/Science publishers, Cambridge, etc.) | Miyoko is a Harvard graduate student; scholarly publishers currently fall through to `Proxy.list`/`final`. Give them an explicit policy (e.g. 美国节点 or 全球加速) so paywalled/library resources route predictably. | High |
| `Orz-3/mini` icons as `orz-3-mini/` (or a pinned copy of `Color/*.png` + `none/qikuo.png`) | **Gap:** the config references 13 icon URLs from `Orz-3/mini` (`Color/Global, Apple, Streaming, StreamingSE, Telegram, Final, Static, HK, TW, JP, SG, US.png` + `none/qikuo.png` profile image) and **none** of them is in the mirror. Without this folder the curated config's icons 404. Needs approval — it is a fourth upstream repo; check its size/licence first. Alternative: re-point `img-url`s at `kop-xiao/img/policy/*` and prune mini entirely. | High |
| `README.md` (repo root) | Attribution, upstream commit pins, update procedure (`git fetch` per folder, diff review before push), and the local-vs-public split (§5). | High |
| Root `.gitignore` | `*.DS_Store`, `*.p12`, `*.mobileconfig`, `local/` (see §5). | High |
| `rulego/Surge/Ruleset/Extra/GenAI/Meta.list` — **only as a reference** | Not needed as a list (subset of `AI.list`), but it documents the Meta/Muse domains. The local Muse rules (§5) can be trimmed against it. | Low |
| Optional, off by default: `Extra/X.list`, `Extra/GeoLoc.list`, `Extra/Crypto.list`, `Extra/PayPal.list`, `Surge/Module/Block/iOSUpdate.sgmodule` | Candidates if Miyoko wants X/Twitter, geo-location services, crypto, PayPal, or iOS-OTA blocking split out. List them in the README as opt-in, do not load by default. | Low |
| `Quantumult/Sample.conf` filter-order note | Already kept as reference (§1.1) — no new file needed. | — |

## 4. Bugs / stale links found (fix regardless of pruning)

1. **`geo_location_checker` URL is stale.** `Orz-3.conf:8` points to
   `https://raw.githubusercontent.com/Orz-3/Orz-3/master/QuantumultX/IP.js`
   (repo `Orz-3`, folder `QuantumultX`), but the cloned repo is
   `Orz-3/QuantumultX` with `IP.js` at its root — the correct raw URL
   would be `…/Orz-3/QuantumultX/master/IP.js`. Verify live; if 404,
   switch to RuleGo's `Quantumult/Script/geo_location_checker.js`
   (what `Sample.conf` uses) or KOP-XIAO's `Scripts/IP_API.js`.
2. **`streaming-ui-check.js` has no policy to report to.**
   Line 35 hard-codes `policy_name = "Netflix"`, but the config defines
   no `Netflix` policy group. Either add a Netflix/流媒体 policy group
   or edit the script's `policy_name` to an existing group
   (e.g. `国际媒体`). Also note the script contacts Netflix, YouTube,
   Disney+, DAZN, Paramount+, Discovery+ and ChatGPT on every run.
3. **RuleGo default-branch trap.** The default branch is a lookalike
   `maѕter` (Cyrillic `ѕ`) containing only a README; the real content is
   on branch `master`. Any future `git clone` / fetch must specify
   `master` explicitly, and the README must warn contributors.
4. **`Task/*.json` mostly invalid JSON** (§2.3) — 20/22 fail strict
   parsing (trailing commas). Do not import blindly.
5. **Ordering is load-bearing, not cosmetic.** `TikTok.list` is loaded
   *before* `!CN.list` so TikTok → 美国节点 while other streaming →
   国际媒体, even though TikTok's domains are all inside `!CN.list`.
   Same for `Direct+` first / `Proxy+` last. Document the order in the
   curated config; do not alphabetise it.

## 5. What must stay LOCAL (never in the public mirror)

- **Miyoko's 13 custom `filter_local` rules** (`muse.ai`, `meta.ai`,
  `meta.com`, `facebook.com`, `fbcdn.net`, `metaaiusercontent.com`,
  `atmeta.com`, `metaaivm.com`, `graph.facebook.com`, `graph.meta.ai`,
  `shortwave.facebook.com`, `hatch-api.meta.ai`, `api.muse.ai` → 美国节点).
  They must live in a **local-only overlay**, e.g. a gitignored
  `local/muse-filter-local.conf` (or a private repo) that is appended to
  the curated config on her device — never committed to
  `miyoko0204/quantumultx-mirror`.
  - Pruning note: 6 of the 13 are already covered by `AI.list`
    (`muse.ai`, `meta.ai`, `meta.com`, `facebook.com`, `fbcdn.net`,
    `graph.facebook.com`) and 3 more by `Proxy.list`; `api.muse.ai` is a
    subdomain of `muse.ai`. The genuinely additive local rules are
    `metaaivm.com`, `metaaiusercontent.com`, `atmeta.com`,
    `graph.meta.ai`, `shortwave.facebook.com`, `hatch-api.meta.ai`.
    Keep the full 13 locally (explicit beats implicit), but the overlay
    can be trimmed to those 6 if she wants it minimal.
- **MITM certificate material.** The local config's `[mitm]` section
  contains a passphrase and a base64 `p12` blob. **These values must
  never appear in the repo, in `ANALYSIS.md`, in commit messages, or in
  screenshots.** The public curated config should ship `[mitm]` with
  `hostname = www.google.cn` only (matching upstream `Orz-3.conf`) and
  instructions to generate/install her own certificate on-device.
  `skip_validating_cert = true` is already in upstream `Orz-3.conf`;
  keep it as-is and do not broaden `hostname`.
- **Node subscriptions / server lists.** `[server_remote]` is empty in
  both upstream and local configs — keep it that way in the public repo.
  No proxy credentials, tokens or subscription URLs belong in the mirror.

## 6. Security notes

- **`resource-parser.js` is the highest-trust file in the mirror.**
  266 KB / 5,740 lines, runs on *every* remote-resource parse
  (`opt-parser=true` on all 14 filters + 4 rewrites), parses node
  subscriptions including passwords, and embeds `shrtm.nu` short links
  and `t.me` channel links in its notification payloads. It is required,
  but: pin it to a reviewed commit (the config currently loads `@master`
  via jsDelivr — floating), and diff it on every update before pushing.
  No live credentials were found in it — the `password=` strings in the
  grep hits are parser code and example comments, not real secrets.
- **Scripts that execute code / phone home.** `streaming-ui-check.js`
  and its 9 sibling check scripts make outbound requests to streaming
  services; `IP*.js` / `geo_location*.js` send the node IP to
  `ip-api.com`. `server-info-plus.js`, `traffic-check.js` query node
  traffic APIs. None of these is loaded by the local config except
  `streaming-ui-check.js` — keep it that way.
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
- **`JS_GetCookie.conf` + `Task/*.json`** (see §2.3): cookie-capture
  rewrites and daily scripts that read QX-stored cookies for JD/Weibo/
  carriers/etc. If Miyoko uses any of them, she should know the scripts
  come from third-party repos (NobyDa, chavyleung, …) and can exfiltrate
  those cookies. No tokens are embedded in the mirrored JSON files
  themselves (checked by grep for `cookie|token|auth|password|session`).
- **`.DS_Store` × 5** in `kop-xiao/` — delete; add to `.gitignore`.
- **No secrets were found in the three clones** (grep for
  `passphrase|p12|api-key|secret|password` excluding `.git/` returns only
  parser code). The only secret material in scope is in the *local*
  config (§5) — which is why §5 exists.

## 7. Proposed curated Quantumult X config outline

A single public file, e.g. `curated/Miyoko.conf` (or repo-root
`QuantumultX.conf`), built from the mirror with **no local secrets**:

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

Local overlay (gitignored, never pushed): `local/` containing the 13
Muse/Meta rules and the MITM `passphrase`/`p12`, applied on-device after
importing the curated config.

## 8. Suggested execution order (after approval)

1. Delete `.DS_Store` × 5; add root `.gitignore` + `README.md`.
2. Prune §2.1–2.3 (GenAI, per-service Streaming/Apple lists, Shadow,
   Surge profile, dragonball/policy/southpark icons, unused KOP scripts,
   Task gallery, JS_GetCookie/JD_TB_price) — or move to `archive/` if
   Miyoko prefers reversible pruning.
3. Add `Reject/Malicious.list` + `Scholar.list` to `filter_remote`;
   mirror `Orz-3/mini` icons (or re-point `img-url`s).
4. Fix the two stale items (§4.1 geo-location URL, §4.2 `policy_name`).
5. Decide the 3 non-mirrored `rewrite_remote` entries (§6).
6. Build `curated/Miyoko.conf` per §7; verify it imports cleanly in
   Quantumult X **before** any push; keep the local overlay separate.
7. Verify upstream licences, then push.
