---
title: Hello, My AI Agent Army
date: 2026-11-15 16:00:00
categories:
  - build
tags:
  - AI
  - Agent
  - Claude-Code
  - workflow
  - skill
---

# 人在開會吃飯睡覺，Agent 還得把目標跑完

會議、客戶討論、吃飯、睡覺——一天能盯著螢幕陪 AI 的時間很少。  
寫功能、修 bug、跑測試，卻常卡在「你一離開，它就停」。

我們也不是很有錢可以無限燒 Token。  
所以要的不是多開幾個聊天窗，而是一支**能自己往下做、會交班、會停、也會省**的 Agent 大軍。

最近在做 AI Project Hub 時，試著讓 Claude Code 不只幫忙寫碼，而是能規劃、派工、實作、驗證，再依結果往下推。踩過坑之後，把有用的 Agent 設定、工程規範與腳本收成 **AI_Agent_Starter**——不是新的 Agent Framework，是基於 Claude Code 原生機制的可攜開發流程。目前還在本地跑，還沒丟上 GitHub。

Cloakly 管的是人鎖在會議室裡；這篇管另一半：**人不在時，軟體交付還能不能往前推**。

<!-- more -->

---

## 一個 Project，底下三個專業角色

實際用下來，編制定成這樣：

```text
Project（主）── 讀 SPEC，拆工項，派工，追進度，處理卡住
├── Product  提出 SPEC 與 UI／UX
├── Dev      在既有架構裡實作、重構、除錯
└── Test     獨立驗證、回歸、缺陷分析
```

**Project 是主協調者，不是第四個平起平坐的子 Agent。**  
Product 提出 SPEC（角色權限、流程、資料／API 契約、正負向驗收）和 UI／UX（畫面狀態、互動流程）；**讀 SPEC、拆成工作單、再派工的是 Project**。Dev 做完一張單寫回結果；Test 獨立驗——PASS 就往下、FAIL 交回 Dev、BLOCKED 記原因，必要時才問人。

典型路徑：

```text
需求 → Project → Product（SPEC／UI／UX）
              → Project 拆單
              → Dev → Test → Project 判斷下一步
```

Product 的 UI／UX **不靠瀏覽器反覆盯畫面微調**——接過，實測太燒 Token；改成守框架官方元件庫，把載入／空／錯／沒權限寫清楚。有截圖或步驟證據才審具體問題。選型建議標 proposed，交回 Project 定案。

Dev 不准自己發明架構：design pattern、clean architecture（或 Profile 寫死的邊界）照走；YAGNI、DRY，該抽再抽，用不到的別先蓋。能進 lint／型別／架構測試的規則就寫進去。

### 為什麼不預設全開平行？

平行看起來比較快，但容易搶同一批檔、規格與程式脫節、測試基準中途飄掉。共用工作目錄時，我優先序列化——**一次一個子 Agent、一張單**。平行是優化手段，不該是預設。

---

## 為什麼收成 Plugin，而不是每個專案複製一份

一開始規則寫在專案的 `CLAUDE.md` 和 `.claude/agents/`。單一專案沒問題；規則越積越多，新專案就要再複製、再改、再維護。

所以 Starter 改用 Claude Code 原生 plugin，責任拆開：

| 放哪 | 幹嘛 |
|------|------|
| **Agents** | 誰做、工具權限、模型與輸出要求 |
| **Skills** | 重複作業怎麼做（工作單、跨 Session 接回） |
| **Hooks** | 可程式化的門禁（秘密檔、停止檢查別自己無限迴圈） |
| **Scripts** | 確定性的事交給 Shell（背景命令、輸出摘要、init、selftest） |
| **專案 CLAUDE.md** | 技術棧、目錄、怎麼測、資料權限——換棧只改這裡 |

通用的是協作流程與交接；產品需求與技術棧留在專案，不硬塞進 Starter。

概念結構大致是：

```text
AI_Agent_Starter/
├── agents/          project / product / dev / test
├── skills/          work-ticket、session-resume
├── hooks/           stop_guard、secret_guard
├── scripts/         init、bgrun、runq、selftest
└── templates/       CLAUDE.md、工作單、PROGRESS.md
```

---

## 規則要放對地方，Prompt 不是萬能

不是每條規則都該寫進提示詞。

「改完要跑相關測試」——適合寫在 Dev 規範。  
「features 不得直接依賴其他 features」——只靠嘴說不夠，ESLint／架構測試／CI 擋才穩。

我現在大概這樣分：

| 類型 | 放哪 |
|------|------|
| 長期工程原則 | 專案 CLAUDE.md |
| 角色分工 | Agents |
| 可重複流程 | Skills |
| 工具前後限制 | Hooks |
| 可機械判定的品質 | Lint／測試／CI |
| 長時間進度 | 工作單＋PROGRESS |

AI 也很容易把「可維護」理解成多蓋一層抽象。所以加了防膨脹：沒重複問題不急著抽；新實作取代舊的就刪掉相容層；不要兩套長期並存。工程品質不是程式越多越專業。

---

## 長時間自主開發，最痛的是交班

這是實戰裡最深的一刀。

前端 Feature-based 遷移時，Dev 撞到回合上限，做了不少卻還沒寫進工作單；Test 也發生過類似情況。單次任務 Dev＋Test 都超過十萬 Token，還因上限要接續。

**不能等「任務結束才寫報告」當唯一存檔。**

後來要求：開工先確認範圍與驗收；里程碑就更新工作單；測完立刻記結果；快撞上限先留下夠接續的狀態；下一個 Agent **先讀單，不靠上一段長對話**。

跨 Session 也拆開記：

- `CLAUDE.md`：長期規則與 Project Profile  
- `PROGRESS.md`：現在做到哪、卡在哪、待你處理  
- 工作單：這張單的證據與下一步  
- （專案可另有）決策紀錄：已拍板的需求與技術取捨  

你開會回來，**直接看 PROGRESS 和工作單**，不用再問進度。

兩支 Skill 就是為這件事：

- **work-ticket**：交接寫在 `docs/handoff/`，嘴上只回路徑和結論  
- **session-resume**：Project 開工只讀最少檔案接回來，不整份重讀規格與日誌  

---

## Token：最大的浪費不一定是模型選錯

一開始以為換較輕的 model／effort 就能省。實際跑下來，不夠。

**反覆讀同一堆資料**——Project 要把範圍講清楚；Agent 只讀這次需要的；決策寫進單；別把整份測試 log 再餵回去。

**整套測試無腦重跑**——小改先跑有關的；動到權限／資料庫／交易再加強；重大重構或 Gate 才完整回歸。Test 要獨立驗，但別無意義重複 Dev 剛跑過的同一輪。

**工具輸出灌爆 context**——`runq.sh` 完整結果進日誌，對話優先拿摘要與關鍵錯誤；`bgrun.sh` 管長命令的逾時與狀態，別叫 LLM 用 `pgrep` 空等猜程序還在不在。

確定性的事交給 Shell；模型負責判斷，不要負責盯進度條。

---

## 踩坑：Stop Hook 自己繞圈子

有次明確說：測完、記進度、停止派工。Agent 確實沒開新工，但 Goal 一直認為整份 SPEC 沒做完，反覆擋 Session 結束——出現 hook 連續擋結束很多次。人沒在開發，檢查本身還在燒 Token。

經驗是：**「任務還沒做完」≠「不准停」。**  
要分得清：可結案、可暫停（狀態留著但允許停）、受阻（記原因再停）、異常（別再進同一無效循環）。

Starter 的 `stop_guard` 就是限制自訂停止檢查反覆阻擋，避免 Hook 自己無限迴圈。但它**管不了** Claude Code `/goal` 內部怎麼判——當時還是得手動 `/goal clear`。  
所以自主 Agent 要有明確停止機制，不只是「會一直跑」。

---

## 安全：限能力，不只提醒小心

`secret_guard` 會攔敏感路徑讀取，但這只是一層，取代不了檔案權限、密鑰管理與執行環境隔離。

基本原則：秘密不進 Starter；專案資訊與通用規則分開；Agent 只拿任務需要的工具；外部系統、刪資料、敏感權限變更走專案自己的授權；文件／測試資料／外部回覆不能改寫系統指令。

**可以自主解一般工程問題，不准自己擴大授權。**

---

## v0.1 驗到哪、還沒驗什麼

目前：4 Agents、2 Skills、2 Hooks、輔助腳本與 init／selftest。建立時 `selftest.sh` 31 項過、`claude plugin validate` 過（僅 LICENSE／icon 警告）；init 不覆蓋既有檔、可重複跑。

還沒驗：在另一個真實專案裡四個 Agent 的載入與調度、完整 Project→Dev→Test 端到端、不同技術棧與權限環境、跟其他已裝 Hooks／Skills 的互相影響。

本地用法大致是 init 目標專案、編好 `CLAUDE.md` Profile，再以 Project 角色載入 plugin——路徑依你機器為準；公開安裝管道之後再說。

---

## 先記著這幾條

1. **不追求最多 Agent，追求責任清楚**——只有真的需要不同專業或獨立驗證才拆。  
2. **狀態進檔案，不靠對話當硬碟**——會不會接得回來，比單次 Session 能撐多久重要。  
3. **可驗證的規則交給工具**——Prompt 給原則；Lint／測試／Hooks／CI 做判定。  
4. **成本是架構的一部分**——切片大小、測試策略、輸出量、模型分工、交接方式都在燒錢。  
5. **自主要有邊界**——知道何時停、何時交、什麼不能自己定。  
6. **Starter 保持通用、輕量**——流程可攜；棧與產品留給專案。

真正該累積的，不只是 AI 寫出來的程式，而是**怎麼讓它穩定、可靠、可控地開發**的方法。Starter 還不是成熟的自主系統，但已把分工、驗收、交班、省 Token、安全停止收成可攜起點。

接下來不是狂加 Agent 或 Skill，而是換專案驗證哪些設計真的有用，再精簡。希望下次開工，不用從頭再教 AI 怎麼開發——站在上一專案的工程經驗上繼續走。

---

參考：[Create plugins](https://code.claude.com/docs/en/plugins) · [Subagents](https://code.claude.com/docs/en/sub-agents) · [Hooks](https://code.claude.com/docs/en/hooks) · [Steering Claude Code](https://claude.com/blog/steering-claude-code-skills-hooks-rules-subagents-and-more)
