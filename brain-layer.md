# 大腦層整合：技術注意事項

> 此文件補充 SKILL.md 大腦層選項的技術細節，供 agent 在規劃實作時參考。

## NotebookLM 整合的現實狀況

NotebookLM 是 Google 提供的多文件 RAG 服務，能讀取 PDF、Google Doc、網頁、YouTube 字幕，並提供綜合判斷、心智圖、podcast 等輸出。

**重要事實：截至本 Skill 發佈時，NotebookLM 沒有官方公開 API。**

這個事實對 agent 整合有重大影響。

### 可能的整合路徑

如果 agent 與使用者選擇 NotebookLM 作為大腦層，必須理解以下三條路徑各自的權衡：

#### 路徑 A：人在迴路

- **做法**：人類指揮官手動使用 NotebookLM 網頁，輸出判斷後，把結果以結構化格式寫入本地檔案（JSON / Markdown），執行層讀此檔案
- **優點**：合法、穩定、不違反 ToS、最像新人類聯盟「**慢思考靠人類**」的精神
- **缺點**：判斷頻率受人類時間限制（建議週級而非日級）
- **適用**：剛開始學架構、希望對齊新人類聯盟原則的人

#### 路徑 B：瀏覽器自動化（CU）

- **做法**：執行層用 Computer Use 或 Playwright 操控 NotebookLM 網頁，自動輸入 prompt、抓取回答
- **優點**：頻率高、半自動
- **缺點**：脆弱（網頁改版會壞）、ToS 風險（Google 可能視為自動化濫用）、需處理登入態
- **適用**：技術探索、私人實驗，**不建議**公開分享或用於穩定產品

#### 路徑 C：替換為其他工具

- **做法**：用 Claude Projects、Perplexity Spaces、或自建 Chroma + LangChain RAG 取代 NotebookLM
- **優點**：有官方 API、整合穩定、可程式化呼叫
- **缺點**：失去 NotebookLM 的 podcast / 心智圖輸出能力
- **適用**：需要把整套系統公開分享、或希望整合穩定的場景

### 推薦選擇

**對 90% 的使用者，建議路徑 A 或路徑 C。**

路徑 B 在技術上可行，但在「**可分享的 Skill**」這個目標下不該被推薦——其他工程師複製你的 SKILL.md 之後會發現他們得自己重建最難的那塊（自動化操控），這不符合本 Skill「**架構樣板**」的定位。

如果 agent 與使用者堅持路徑 B，請在實作中明確標註「**這部分為使用者私有實作，非本 Skill 的公開介面**」。

## 大腦層的介面設計

不論選哪條路徑，大腦層對外暴露的介面應一致：

```python
class BrainLayer(Protocol):
    def request_judgment(
        self,
        question: str,
        context: dict | None = None,
    ) -> Judgment:
        """請大腦層提供判斷"""

    def update_knowledge(
        self,
        sources: list[Source],
    ) -> None:
        """更新大腦層的知識庫
        注意：此操作必須通過篩選權檢查（人類指揮官手動審核）
        """
```

`Judgment` 結構建議：

```python
@dataclass
class Judgment:
    summary: str          # 自然語言摘要
    signals: list[Signal] # 結構化訊號
    reasoning: str        # 人類可讀的理由
    valid_until: datetime # 此判斷的失效時間
    sources: list[str]    # 引用的資料來源 ID
```

`valid_until` 是大腦層自己宣告的——它應該知道自己的判斷適用多久。執行層在每次決策前都應檢查這個欄位。

## 知識庫的篩選權實作

無論大腦層選哪條路徑，知識庫的內容注入**必須**經過人類審核（見 `principles.md` 篩選權）。

建議實作：

1. 設一個 `inbox/` 目錄，agent 把候選資料放進去（爬取的、推薦的、引用的）
2. 指揮官每週固定時段檢視 `inbox/`，手動把確認要進入大腦層的資料移到 `accepted/`
3. 只有 `accepted/` 裡的資料會被推送到大腦層（NotebookLM 上傳、RAG 索引、Claude Projects 加入）

這個流程在實作上只是兩個資料夾的差異，但在哲學上是新人類聯盟對 dataism 的具體反抗。

---

*這份文件不指定 agent 該選哪條路徑——選擇是使用者與 agent 共同決定的。但無論選哪條，篩選權的實作不可省略。*
