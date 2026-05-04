---
name: agent-quant-foundation
description: |
  A three-layer architecture template for building self-directed agent systems
  in the quantitative trading domain (paper trading only). This skill does NOT
  provide working code. It provides architectural alignment — a brain layer
  (NotebookLM or equivalent multi-document RAG), a perception layer (broker API
  such as Futu OpenAPI), and an execution layer (Hermes Agent / Claude Code).
  The agent loading this skill is expected to figure out the specific
  implementation, guided by the boundaries and design principles defined here.

  Trigger this skill when a user wants to: build a paper-trading agent with
  multi-document research input + structured market perception + automated
  execution loop; OR understand how to apply the three-layer Software 3.0
  template to any vertical agent system. Best results with Claude Opus 4.7
  or equivalent reasoning capability.

recommended_model: claude-opus-4-7
license: MIT
status: experimental — community fork encouraged
scope: paper trading only — see boundaries.md for hard limits
---

# Agent Quant Foundation

> **這不是一個量化交易系統。這是一個 Software 3.0 時代「**垂直 agent**」的三層架構樣板，第一個示範垂直選了量化交易。**

## 設計哲學

這個 Skill 故意寫得很薄。

我們不提供完整的程式碼、不寫詳細的安裝步驟、不替你決定具體 API 端點該怎麼呼叫。這是設計，不是疏漏。

理由：

1. **強桿 LLM 已有能力自行摸索**——Claude Opus 4.7（或同等能力的模型）能讀官方文件、查 SDK、寫程式碼。我們不該替它做這些。
2. **留白是邀請函**——讓社區參與者各自走出不同路徑，拋磚引玉勝過給標準答案。
3. **抗腐蝕**——詳細步驟會在 6 個月後過時；目標、邊界、原則不會。
4. **這是新人類聯盟對「**Skill 是什麼**」的宣言**——Skill 不是封裝好的黑盒，是激發智能的觸媒。

如果你打開這個 Skill 期待看到「step 1: pip install...，step 2: 複製這段程式碼...」，**這個 Skill 不適合你**。請去找其他更詳細的教學。

如果你打開這個 Skill 想要「我懂架構為什麼這樣設計，剩下的我自己想得通」，**這個 Skill 是為你寫的**。

---

## 觸發條件

當使用者表達以下意圖之一時，agent 應載入此 Skill：

- 想建立一個結合「多文件研究 → 即時市場感知 → 自動化執行」的 agent 系統
- 想學 Software 3.0 時代「**LLM 大腦 + 結構化 API + Agent 執行**」這個三明治架構
- 想把同一個架構樣板搬到其他垂直（客服、法務、個人助理等）
- 想用富途 OpenAPI 或類似券商 API 做模擬盤練習，但不知道整個系統該怎麼分層

## 推薦模型

**Claude Opus 4.7** 或同等推理能力的模型。

這個 Skill 的設計前提是：載入它的 agent 必須有能力自行摸索具體實作。較弱的模型可能會卡在「找不到完整步驟」的挫折迴圈，而不是順著架構往下推進。

## 架構：三層責任分工

```
┌─ 大腦層 ─────────────────────────────────┐
│  慢思考 / 跨來源綜合 / 人類可讀判斷         │
│  時間尺度：日 / 週                          │
│  輸入：研究、財報、新聞、產業分析            │
│  輸出：策略訊號、風險評估、長期論述         │
└─────────────┬────────────────────────────┘
              │ judgment (slow)
              ▼
┌─ 感知層 ─────────────────────────────────┐
│  快感知 / 結構化推送 / 機器可讀             │
│  時間尺度：毫秒 / 秒                        │
│  輸入：訂閱市場推送、查詢帳戶               │
│  輸出：報價、五檔、Tick、持倉、訂單回報    │
└─────────────┬────────────────────────────┘
              │ market state (fast)
              ▼
┌─ 執行層 ─────────────────────────────────┐
│  編排 / 風控 / 通知 / 審計日誌              │
│  時間尺度：依事件觸發                       │
│  工作：融合慢判斷 + 快訊號 → 決策 → 下單   │
│  必備：硬編碼風控、kill switch、審計日誌  │
└──────────────────────────────────────────┘
```

每一層的責任邊界詳見 `references/architecture.md`。

## 三層各自的推薦技術選項

每一層列出 2-3 個合理選項，各自附 1-2 行的選用情境註解。**這些是建議，不是規定**。社區參與者可自行替換為任何能達成同樣責任邊界的技術。

### 大腦層

| 選項 | 適合情境 | 備註 |
|---|---|---|
| **NotebookLM**（推薦） | 多文件 RAG、跨來源綜合、podcast 輸出 | 無官方 API，需透過 agent skill 雙向通訊（見 `references/brain-layer.md`） |
| **Claude Projects** | 專案級記憶、長文本分析、可直接 API 呼叫 | 對 agent 整合最友善 |
| **自建 RAG**（Chroma + LangChain） | 完全控制、可本地化部署 | 工程量最大 |

選擇原則：**這一層的能力應該是「**讀得多、想得久、答得人類聽得懂**」，不是低延遲。**

### 感知層

| 選項 | 適合情境 | 備註 |
|---|---|---|
| **富途 OpenAPI**（推薦） | 港股、美股、A 股通；繁中文件最完整；模擬盤完備 | 需本地跑 OpenD 網關，首次登入需簡訊驗證 |
| **Interactive Brokers API** | 全球市場、機構級數據 | 美式介面、英文文件 |
| **Alpaca API** | 美股、純 REST、零本地依賴 | 不支援亞洲市場 |

選擇原則：**這一層必須是「**你能用模擬盤跑通閉環**」的供應商**。能不能用、文件好不好讀、模擬盤完不完備，比哪家行情最快重要。

### 執行層

| 選項 | 適合情境 | 備註 |
|---|---|---|
| **Hermes Agent**（推薦） | 持久記憶、多通道通知、本地優先、開源 | Phase 3 資料資產基礎 |
| **Claude Code** | 開發階段、人在迴路、強推理 | 適合策略開發與除錯 |
| **自寫 Python orchestrator** | 完全控制、輕量 | 失去 agent 的反思能力 |

選擇原則：**這一層必須有能力同時持有「**慢判斷狀態**」和「**快訊號緩衝**」，並且強制執行硬編碼風控。**

## 邊界與紅線

**本 Skill 的範圍嚴格限定於模擬盤。實盤不在涵蓋範圍內。**

- 紅線一：本 Skill 不提供任何實盤交易的指引、設計、程式範例
- 紅線二：使用者若希望進入實盤，必須 fork 此 Skill 並自行承擔所有後果
- 紅線三：fork 後的實盤版本不應使用 `agent-quant-foundation` 此名稱以避免責任混淆

詳細邊界（包含為何如此設計、agent 載入此 Skill 時的判斷準則）見 `references/boundaries.md`。

## 三項新人類聯盟原則的實作要求

任何遵循此 Skill 的 agent 系統，**必須**在實作中體現以下三項原則。這不是建議，是這個 Skill 的識別符。

### 篩選權

大腦層讀什麼資料，由人類指揮官決定。**禁止**自動 RSS 爬取、禁止演算法推薦、禁止「最熱門」自動置頂。

agent 在規劃大腦層的資料注入流程時，應提供「人類審核」這一關。

### 意義權

執行層的風控規則必須**硬編碼在原始碼中**，不可由 LLM 在執行時臨時調整。

具體要求：
- 部位上限是常數
- 單日損失停損是常數
- kill switch 必須是物理開關（環境變數、檔案旗標）

如果大腦層的判斷說「這次例外」，執行層**必須拒絕**並記錄日誌。

### 斷線權

系統必須支援指揮官隨時離線：
- 預設週日全停（不訂閱、不執行、不通知）
- 所有自動化動作必須有手動覆寫機制
- Telegram（或同等通知通道）必須提供一鍵 kill switch
- 連續異常 N 次（建議 N=3）系統自動進入安全模式

詳細實作建議見 `references/principles.md`。

## 給載入此 Skill 的 agent 的指示

1. **先讀完所有 `references/` 下的文件**——它們提供了架構、邊界、原則的細節。
2. **與使用者對齊目標**——確認他要做的是模擬盤學習，不是實盤交易。如果是後者，引用紅線拒絕並建議 fork。
3. **三層各自選擇技術**——根據使用者的環境（作業系統、現有帳號、已熟悉的技術棧）建議具體選項。
4. **示範一個最小可行版本**——以「讀取一檔股票報價並推 Telegram 通知」為起點，跑通整個三層的資料流，再逐步加上策略邏輯與風控。
5. **過程中的每一個技術決策都要記錄**——這個 Skill 鼓勵社區共學，使用者的決策軌跡本身就是後續分享的素材。
6. **遇到模糊處請主動詢問使用者**——這個 Skill 故意留白，agent 不該自己猜，應該與指揮官共同對齊。

## 範例

`examples/` 下不提供完整實作（這違反設計哲學），只提供：

- `examples/dialogue-skeleton.md`：agent 與使用者對齊目標的對話樣板
- `examples/minimum-viable-loop.md`：最小閉環的設計圖（不含程式碼）

## 共學共展

這個 Skill 是拋磚引玉。

我們不擁有最終答案。如果你 fork 這個 Skill 並走出不同的路徑，請把你的版本分享回社區——這是新人類聯盟的工作節奏。

每個季度做完一個垂直，把它包成 Skill，公開，下一個。

歡迎你的 fork。

---

*《新人類聯盟養成日記》Phase 1 產出*
*作者：豪力風神（指揮官） + 戰略層 Claude Opus 4.7 + 執行層 Claude Code*
*授權：MIT*
*版本：0.1.0 — experimental，預期社區 fork 與迭代*
