# BrainThinking

個人知識庫，採用 **LYT（Linking Your Thinking）** 框架的 **ACE** 結構整理。
總入口見 [Home](./Home.md)。

## 結構（ACE）

```
BrainThinking/
├── Home.md                 # 總入口 MOC
├── Atlas/                  # 知識層（學習模式）
│   ├── Maps/               # 主題地圖 MOC
│   ├── Things/             # 概念 / 工具 / 框架 / 模板
│   ├── Statements/         # 自己的觀點與洞察（最高價值）
│   └── Sources/            # 外部素材摘要（書 / 文章 / skill / 貼文）
├── Calendar/               # 時間層（反思模式）
│   └── Journal/            # 自省 / 日記 / 回顧
└── Efforts/                # 執行層（專案模式）
    └── Projects/           # 有明確終點的專案
```

## 三種心智空間

| 空間 | 核心問題 | 心智模式 |
|------|---------|---------|
| **Atlas** | 這是什麼？ | 學習 |
| **Calendar** | 什麼時候發生的？ | 反思 |
| **Efforts** | 我在做什麼？ | 執行 |

## 筆記類型（Atlas 內）

- **Things** — 這是什麼？（概念、工具）
- **Statements** — 我怎麼想？（洞察、原則）
- **Sources** — 別人怎麼說？（外部素材摘要）

## 命名規則

- **知識筆記**：用語意化檔名（如 `能動性.md`、`ecsctl.md`），利於 `[[wikilink]]` 與人閱讀。
- **日記 / 時間軸**：`YYYY-MM-DD-語意標題.md`。
- **禁止冒號 `:`**：檔名一律不得含 `:`（Windows / NTFS 無法建立檔案）；時間一律用連字號 `-`。
- 時間資訊放進 frontmatter 的 `created` 欄位，不放在檔名的機器時間戳。

## Frontmatter 範本

每則筆記開頭套用（完整見 [TEMPLATE.md](./TEMPLATE.md)）：

```yaml
---
up: ["[[某某 MOC]]"]        # 上級地圖（可多個）
collection: ["[[Things]]"]  # 筆記類型
related: []                 # 橫向相關筆記
created: 2026-07-01         # 建立日期
source: manual              # 來源：manual / telegram / claude-code / github-actions
says: "一句話核心觀點"
---
```

## MOC（Maps of Content）

MOC 是「地圖筆記」，內含指向其他筆記的連結，按思路排列。一則筆記可同時出現在多個 MOC。
新主題累積數則筆記後，在 `Atlas/Maps/` 建立對應 MOC，並在 [Home](./Home.md) 掛上入口。
