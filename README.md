# Agent Quant Foundation

> Software 3.0 時代的散戶基礎設施 — 三層架構樣板
>
> 拋磚引玉的 Skill，邀請社區共學共展

## 這是什麼

一個故意寫得很薄的 Skill。

它不提供完整程式碼。它提供的是**架構**——一個三層樣板（大腦 + 感知 + 執行），讓你（或你的 agent）依此自行摸索具體實作。

第一個示範垂直選了「**量化交易模擬盤**」，但這個樣板本身可以搬到任何垂直 agent：客服、法務、個人助理、DevOps……骨架完全一樣。

## 適合誰

✅ 想學 Agent 架構的工程師
✅ 想理解 Software 3.0 三明治樣板的人
✅ 想用這個樣板搬到其他垂直的人

❌ 想直接賺錢的人
❌ 完全不懂 Python 的人
❌ 希望「**現成可跑的程式碼**」的人

## 如何使用

### 給 Agent

把整個資料夾交給 Claude Opus 4.7（或同等推理能力的模型），讓它讀 `SKILL.md`。Skill 內的指示會告訴 agent 怎麼與你協作。

### 給人類

依以下順序閱讀：

1. `SKILL.md`（入口）— 5 分鐘，了解整體
2. `references/architecture.md` — 10 分鐘，了解三層責任邊界
3. `references/principles.md` — 10 分鐘，了解三項新人類聯盟原則
4. `references/boundaries.md` — 5 分鐘，了解紅線
5. `examples/minimum-viable-loop.md` — 10 分鐘，了解從哪起步

讀完約 40 分鐘。然後你就有足夠的上下文跟你的 agent 對話了。

## 紅線

**本 Skill 嚴格限定於模擬盤（paper trading）。**

實盤不在涵蓋範圍。要實盤請 fork 並更名（詳見 `references/boundaries.md`）。

## 推薦模型

**Claude Opus 4.7** 或同等推理能力的模型。

較弱的模型可能會卡在「找不到完整步驟」的挫折迴圈。本 Skill 設計上預期載入它的 agent 有能力自行摸索。

## 共學共展

這個 Skill 是拋磚引玉。

我們不擁有最終答案。如果你 fork 並走出不同路徑，請把你的版本分享回社區——這是新人類聯盟的工作節奏。

每個季度做完一個垂直，把它包成 Skill，公開，下一個。

歡迎你的 fork。

## 相關閱讀

- 〈Software 3.0 的散戶基礎設施：一份新人類聯盟養成日記〉— 本 Skill 的設計理念白皮書
- 《新人類聯盟養成日記》系列 — 持續更新的實戰記錄

## 授權

MIT License

## 版本

0.1.0 — experimental
預期社區 fork 與迭代

---

*指揮官：豪力風神 ｜ 戰略層：Claude Opus 4.7 ｜ 執行層：Claude Code、Hermes Agent*
