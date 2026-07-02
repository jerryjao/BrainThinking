# INBOX_SCHEMA — 擷取資料架構

Telegram Agent（及其他自動寫入端）記錄到 BrainThinking 時的**資料契約**。
目的：在擷取當下多寫一點結構化 metadata，讓後續 LYT 整理從「讀內文猜分類」變成「照欄位搬檔」。

整理端如何消化這些檔案，見 [AGENTS.md](../AGENTS.md)。

---

## 一、落地位置與檔名

```
_inbox/2026-06-20T09-30-15Z-<簡短slug或capture_id>.md
```

- 一律寫進 **`_inbox/`**（排序在最前，整理 agent 一眼看到待整理清單）。
- 檔名 ISO8601 時間戳，**冒號一律用連字號 `-`**（Windows / NTFS 無法建立含 `:` 的檔案）。
- 時間戳後接簡短 slug 或 `capture_id`，避免同秒衝突。

## 二、Frontmatter 資料架構

```yaml
---
# ── 擷取時必填（bot 一定拿得到）──────────────
created: 2026-06-20T09-30-15Z   # ISO8601；冒號改連字號
source: telegram                # 固定 telegram（或 github-actions / claude-code）
status: inbox                   # 待整理標記；整理 agent 據此掃描
capture_id: tg-1780681126385    # telegram message id，去重 + 可回溯

# ── 擷取時選填（有就填，直接幫後續分類）──────
title: ""                       # 使用者若有給標題
type: ""                        # 粗猜：things | statements | sources | journal | project | transient
says: ""                        # 一句話摘要（bot 可自動生成）
url: ""                         # 內容來自貼文/文章/repo/skill 時的原始連結
project: ""                     # 明確屬於某專案時填專案名（如 lucifer-claw）
tags: []                        # 關鍵字
sender: jerry                   # 誰送的（多人使用時有用）
---

（原始訊息內容，原封不動）
```

## 三、欄位定義

| 欄位 | 必填 | 說明 | 對後續 LYT 的用途 |
|------|:----:|------|-------------------|
| `created` | ✅ | 擷取時間 ISO8601，**無冒號** | 直接抄進最終卡片，不用從檔名反推 |
| `source` | ✅ | 來源系統 | 抄進卡片 `source` |
| `status` | ✅ | 固定 `inbox`；整理完由 agent 改 `filed` 或移除 | 掃 `_inbox/` = 全部待辦，取代「猜哪些是新檔」 |
| `capture_id` | ✅ | 原始訊息 id | 重複送出去重，避免產生兩張卡片 |
| `type` | ⬜ | 粗略分類（見下方允許值） | 決定搬到哪個 ACE 資料夾；`transient` 直接刪 |
| `says` | ⬜ | 一句話摘要 | 直接當卡片 `says` |
| `url` | ⬜ | 原始連結 | 判定 Sources 類的關鍵訊號；保留出處 |
| `project` | ⬜ | 專案名 | 有值就併進 `Efforts/Projects/<project>.md` |
| `title` | ⬜ | 標題 | 有的話當語意化檔名來源 |
| `tags` | ⬜ | 關鍵字 | 輔助分類與日後檢索 |
| `sender` | ⬜ | 送出者 | 多人情境辨識 |

### `type` 允許值（固定六種）

| 值 | 意義 | 整理後落點 |
|----|------|-----------|
| `things` | 概念 / 工具 / 框架 / 模板 | `Atlas/Things/` |
| `statements` | 使用者自己的觀點 / 洞察（**最高價值**） | `Atlas/Statements/` |
| `sources` | 外部貼文 / 文章 / repo / skill 摘要 | `Atlas/Sources/` |
| `journal` | 自省 / 日記 | `Calendar/Journal/` |
| `project` | 某專案的紀錄 / 連結 | `Efforts/Projects/`（同專案合併） |
| `transient` | 天氣 / 比分 / 股價 / 測試等拋棄式 | **刪除**（整理 agent 直接 `git rm`） |

## 四、實例

**① 分享一則貼文（→ Sources）**
```yaml
---
created: 2026-06-20T09-30-15Z
source: telegram
status: inbox
capture_id: tg-1001
type: sources
says: "Agent 取得工具的優先順序：LLM 生成 > CLI > Skill > MCP"
url: https://www.facebook.com/groups/xxx/posts/yyy/
tags: [agent, 工具鏈, claude-code]
---
（貼文原文…）
```

**② 個人體悟（→ Statements）**
```yaml
---
created: 2026-06-20T22-10-00Z
source: telegram
status: inbox
capture_id: tg-1002
type: statements
says: "事情慢慢來，才是最快"
tags: [習慣, 能動性]
---
（心得原文…）
```

**③ 專案紀錄（→ 併進 Efforts/Projects/lucifer-claw.md）**
```yaml
---
created: 2026-06-20T14-00-00Z
source: telegram
status: inbox
capture_id: tg-1003
type: project
project: lucifer-claw
url: https://github.com/jerryjao/lucifer-claw
says: "整理版寫入 artifacts/4680139808/result.md"
---
（內容…）
```

## 五、最低可行 vs 理想

- **最低限度**（bot 改動最小）：只寫 `created`（無冒號）+ `source` + `status: inbox` + `capture_id` + 原文 → 整理已好做很多。
- **理想**：再補 `type` + `says` + `url`/`project` → 後續幾乎全自動搬檔。

## 六、相容性備註

- 舊格式筆記（無 frontmatter、放在 `New/`、根目錄等）**仍會被整理 agent 處理**，只是需要人／AI 讀內文判斷分類。本 schema 只是讓「之後」的擷取更好處理，不強制回溯改舊檔。
- 若 bot 暫時無法寫進 `_inbox/`，退而求其次至少遵守「檔名無冒號」與「補 `created`」兩條。
