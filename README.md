# quantumultx-mirror

Personal mirror of three public Quantumult X / Surge rule & script repos, so
Miyoko's Quantumult X config can pull rules and scripts from her own GitHub
account instead of directly from the original authors' repos.

> ⚠️ This repo mirrors **public upstream content only**. Do NOT add personal
> config files, proxy node subscriptions, MITM certificates (`.p12`) or
> passphrases here — those stay local only.

## Layout

| Folder | Upstream | Contents |
|---|---|---|
| `orz-3/` | [Orz-3/QuantumultX](https://github.com/Orz-3/QuantumultX) (`master` @ `a014cd9`) | `Orz-3.conf` base config, Bili/YouTube/TikTok configs, `Task/*.json` BoxJS subscriptions, `IP.js`, `Bili_Auto_Regions.js` |
| `kop-xiao/` | [KOP-XIAO/QuantumultX](https://github.com/KOP-XIAO/QuantumultX) (`master` @ `cce2996`) | `Scripts/resource-parser.js`, `Scripts/streaming-ui-check.js` and other check scripts, `QuantumultX_Profiles.conf`, rule lists |
| `rulego/` | [ConnersHua/RuleGo](https://github.com/ConnersHua/RuleGo) (`master` @ `a7e5c4b`) | `Surge/Ruleset/*` domain lists (Direct/Proxy/AI/Streaming/Apple/Reject…), `Surge/Module/*`, `Quantumult/`, `Shadow/` |

Mirrored 2026-11-17. See `ANALYSIS.md` for the prune / supplement plan
(what to keep, remove and add — approval needed before any pruning).

## Notes

- RuleGo's **default branch is a lookalike branch `maѕter`** (Cyrillic "ѕ")
  containing only a README. The real content lives on branch **`master`** —
  this mirror tracks `master`.
- `kop-xiao/img/` PNG icons (policy icons, dragonball/southpark images) are
  **not** in this mirror: the GitHub connector's snapshot push accepts
  UTF-8 text files only, no binaries. They are decorative only and are not
  referenced by the Quantumult X config. To add them, push from a local
  `git` checkout with your own GitHub credentials.
- `Orz-3/mini` (policy icons referenced by `img-url=` in the config) is a
  separate repo and is **not** mirrored yet — see `ANALYSIS.md`.
- Upstream updates are pulled on a schedule (see the `quantumultx-mirror-sync`
  cron); the mirror does not auto-update in real time.

## Use in Quantumult X

Replace upstream raw URLs with your own mirror, e.g.:

- `https://raw.githubusercontent.com/ConnersHua/RuleGo/master/Surge/Ruleset/Proxy.list`
- → `https://raw.githubusercontent.com/miyoko0204/quantumultx-mirror/main/rulego/Surge/Ruleset/Proxy.list`

(Replace `main` with the repo's actual default branch if it differs.)
