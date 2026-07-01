# AGENTS.md — BrainThinking 維護指南

給在此 repo 工作的 AI agent（Claude Code / Codex / 其他）。
本知識庫採用 **LYT（Linking Your Thinking）** 的 **ACE** 結構。人類讀者請先看 [README](./README.md)、入口見 [Home](./Home.md)。

---

## 一、這個 repo 是什麼

一個**由自動化寫入 + 定期人工整理**的個人知識庫：

- **自動寫入端**（Telegram bot、GitHub Actions、其他 Claude Code session）會不定時把新筆記丟進來，通常是**舊格式**：時間戳檔名（如 `2026-06-20T09-30-00.000Z.md`）、放在 `New/`、`notes/`、`telegram/`、`artifacts/` 或根目錄、且**沒有 frontmatter**。
- 我們**不修改寫入端**（那是別的 repo 的 workflow）。改為**定期由 agent 把這些新檔重新歸檔進 ACE 結構**。

**你（agent）的核心任務**：當使用者要求「整理 / 更新 / 歸檔 BrainThinking」時，執行下方〈定期整理流程〉。

---

## 二、目標結構（ACE）

```
BrainThinking/
├── Home.md                 # 總入口 MOC
├── Atlas/                  # 知識層（學習模式）
│   ├── Maps/               # 主題地圖 MOC
│   ├── Things/             # 概念 / 工具 / 框架 / 模板
│   ├── Statements/         # 使用者自己的觀點與洞察（最高價值）
│   └── Sources/            # 外部素材摘要（書 / 文章 / skill / 貼文）
├── Calendar/               # 時間層（反思模式）
│   └── Journal/            # 自省 / 日記 / 回顧
└── Efforts/                # 執行層（專案模式）
    └── Projects/           # 有明確終點的專案
```

**「未歸檔區」**：任何**不在** `Atlas/`、`Calendar/`、`Efforts/` 底下，且**不是** `Home.md`／`README.md`／`TEMPLATE.md`／`AGENTS.md` 的 `.md` 檔，都視為**待整理的新筆記**。

---

## 三、命名與 Frontmatter 規範（硬性）

### 檔名
- **知識筆記**：語意化檔名（如 `能動性.md`、`ecsctl.md`），利於 `[[wikilink]]`。
- **日記／時間軸**：`YYYY-MM-DD-語意標題.md`。
- **絕對禁止冒號 `:`**：Windows/NTFS 無法建立含冒號的檔案（本 repo 踩過此坑）。時間一律用連字號 `-`。
- 機器時間戳不放檔名，放進 frontmatter 的 `created`。

### Frontmatter（每張卡片開頭，範本見 [TEMPLATE.md](./TEMPLATE.md)）
```yaml
---
up: ["[[某某 MOC]]"]        # 上級地圖，可多個；沒有就 []
collection: ["[[Things]]"]  # 筆記類型（見下）
related: []                 # 橫向相關筆記
created: 2026-06-20         # 原筆記的日期（取自原檔時間戳）
source: telegram            # 來源：telegram / github-actions / claude-code / manual
says: "一句話核心觀點"
---
```

### 筆記類型（`collection`）
- `[[Things]]` — 概念、工具、框架、模板（「這是什麼？」）
- `[[Statements]]` — 使用者自己的觀點、洞察、原則（「我怎麼想？」）**最高價值，優先辨識**
- `[[Sources]]` — 外部素材（貼文 / 文章 / repo / skill）的摘要（「別人怎麼說？」）
- Journal 類放 `Calendar/Journal/`，`collection` 可標 `[[Statements]]`

---

## 四、定期整理流程（agent 逐步照做）

> 每次整理**開一個新分支、走 PR**（main 有分支保護，見〈六、環境注意事項〉）。

### 步驟 1：找出待整理的新筆記
列出「未歸檔區」的所有 `.md`（定義見第二節）。若沒有，回報「無待整理項目」即可結束。

### 步驟 2：逐檔分類與處置
對每個新檔，判斷屬於下列哪一種：

| 判斷 | 處置 |
|------|------|
| **時效性/拋棄式**：天氣、比分、股價、純測試檔（`test-write…`）、只有「請記錄這個」之類無實質內容的 meta 檔 | **`git rm` 刪除**（git 歷史仍可查） |
| **使用者的觀點/洞察** | → `Atlas/Statements/語意名.md`，`collection: [[Statements]]` |
| **概念/工具/模板** | → `Atlas/Things/語意名.md`，`collection: [[Things]]` |
| **外部貼文/文章/repo/skill 的摘要** | → `Atlas/Sources/語意名.md`，`collection: [[Sources]]` |
| **自省/日記** | → `Calendar/Journal/YYYY-MM-DD-語意名.md` |
| **某專案的紀錄/連結** | → `Efforts/Projects/專案名.md`（同專案多則就**合併**進同一張卡片） |

處置細節：
1. `git mv` 舊檔到新路徑與語意化檔名（保留歷史）。
2. 在檔案開頭補上 frontmatter：`created` 取自原檔時間戳、`source` 依來源、`says` 濃縮一句、`collection`/`up` 依分類。
3. 內容若無 H1 標題可補一個；不要竄改原意。

### 步驟 3：更新 MOC 與 Home
- 新卡片若屬既有主題，把 `[[卡片名]]` 加進對應 `Atlas/Maps/*.md` 的清單，並在 frontmatter 的 `up` 填該 MOC。
- 累積某新主題 3 則以上卡片時，**新建一個 MOC**（`Atlas/Maps/主題 MOC.md`），並在 [Home](./Home.md) 掛上入口。
- 沒歸入任何 MOC 的卡片，至少在 [Home](./Home.md) 對應區塊補一個連結，避免孤島。

### 步驟 4：驗證連結
確認所有作用中的 `[[wikilink]]` 都對得上實際檔名（CJK 檔名注意；HTML 註解 `<!-- -->` 內的連結是未來 stub，可忽略）。

### 步驟 5：提交
- 全部變更 `git add -A`，commit（訊息用繁體中文，說明本次歸檔了哪些）。
- push 到整理分支、開 PR、回報 PR 連結給使用者由其合併。

---

## 五、分類判斷原則（辨識訣竅）

- **優先抓 Statements**：使用者自己想通的比喻、原則、體悟最有價值（例：「事情慢慢來，才是最快」）。別只當成一般筆記埋掉。
- **Sources vs Things**：講「某個外部東西的摘要」→ Sources；講「一個可複用的概念/工具本身」→ Things。
- **時效資料直接刪**：使用者已決策，天氣/比分/測試檔不留（不進 `_archive`）。
- **同專案合併**：不要為 lucifer-claw 的每次紀錄各開一張卡片，併進 `Efforts/Projects/lucifer-claw.md`。

---

## 六、環境注意事項（踩過的坑）

1. **分支保護**：`main` 有兩個 ruleset（`protect-main` 要求走 PR；`push by app` 限制只有指定 App 與 Repository admin 能更新）。**一律走 PR**，合併需具 admin bypass 權的帳號。
2. **git 帳號**：具寫入權的是 GitHub 帳號 **`jerryjao`**（非 `RLJerryJao`）。push 前若遇 403，執行 `gh auth switch --user jerryjao && gh auth setup-git`。
3. **禁用冒號檔名**：見第三節。若從遠端 checkout 到含冒號的舊檔導致 checkout 失敗，設 `core.protectNTFS false` 後 `read-tree`、對該檔 `--skip-worktree`，再改名成合法檔名。
4. **大小寫衝突**：此 repo 曾同時存在 `New/` 與 `new/` 兩個 index 路徑（Windows 檔案系統看不出來）。`git mv` 若報 `not under version control`，先用 `git ls-files | grep -i` 確認實際大小寫。
5. **換行**：`.md` 用 UTF-8 + LF。
```
