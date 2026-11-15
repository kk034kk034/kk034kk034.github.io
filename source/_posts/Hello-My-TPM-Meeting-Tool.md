---
title: Hello, My TPM Meeting Tool
date: 2026-11-15 15:00:00
categories:
  - build
tags:
  - PM
  - TPM
  - meeting
  - cloakly
---

# TPM 開工第一個工具：Cloakly

寫計劃、拆 WBS、整理風險表——這些現在都能靠 AI 加速。  
真正卡住我的，是**會議**：人的時間被鎖在會議室裡，而且幾乎整天都在開不同的會。

計劃可以晚上補；會議當下錯過的細節、決議、待辦，事後很難靠回憶救回來。

所以我做了 [Cloakly](https://kk034kk034.github.io/cloakly/)：不是又一個錄音轉文字，而是以**專案**為單位，把會議紀錄、行動項、即時問答、相關檔案輔助收在同一個地方。

<!-- more -->

---

## 為什麼是「會議」最痛

一天開四五場、每場換一個專案／客戶／議題時，痛點通常是：

- **切換成本高**：上一場還在談驗收，下一場已經是售前；腦內 context 來不及換
- **紀錄跟不上**：邊聽邊記一定漏；會後再補 MOM，待辦又飛掉
- **被問到就要答**：客戶／主管當場問進度、範圍、上次承諾，你需要秒切回相關檔案與歷史會議
- **會開完不等於有行動**：沒有 owner、期限、下一步，會議等於白開

AI 可以幫我寫文件；**不能替我坐在會議裡**。所以工具要幫的是：紀錄、產生行動、即時問答、快速切換專案脈絡。

---

## 所以 Cloakly 做成「專案」而不是「單場會議」

每場會議都掛在某個專案底下。專案裡有文件、歷次逐字稿、筆記、會議紀錄——之後才能做有依據的問答（RAG），而不是每場會議從零開始。

開會前選定專案資料夾，規格、時程、Issue、簡報會進知識庫；專案首頁就能看狀態、會議、問答，並直接開始會議。問「現況怎樣」「上次承諾什麼」，答案可以帶回來源片段，而不是憑印象。

<div style="display:flex; flex-wrap:wrap; gap:12px; align-items:flex-start; margin:1em 0;">
  <div style="flex:1 1 220px; max-width:280px;">
    {% asset_img 05-projects.png "Cloakly：專案列表與跨專案看板" %}
  </div>
  <div style="flex:1 1 220px; max-width:280px;">
    {% asset_img 06-home.png "Cloakly：專案首頁（狀態、會議、問答、開始會議）" %}
  </div>
</div>

---

## 會議中到會後：逐字稿、輔助回答、行動項

現場我需要同時做三件事：聽、記、偶爾被點名回答。Cloakly 在會議進行中可以串流逐字稿、寫現場筆記，被問到時依專案文件 + 當下逐字稿給建議回覆。

會開完更重要的不是長逐字稿，而是：這場定了什麼、誰要做什麼、還有哪些 open question。結束後會整理摘要、決議與待辦，讓產出接回專案，而不是散落在聊天室。

沒有專案脈絡，即時回答就只是通用聊天；有了檔案與歷史會議，才叫**專案會議助手**。

<div style="display:flex; flex-wrap:wrap; gap:12px; align-items:flex-start; margin:1em 0;">
  <div style="flex:1 1 220px; max-width:280px;">
    {% asset_img 13-session.png "Cloakly：會議中逐字稿與建議回覆" %}
  </div>
  <div style="flex:1 1 220px; max-width:280px;">
    {% asset_img 08-meeting.png "Cloakly：會後會議紀錄（決定與待辦）" %}
  </div>
</div>

---

## 下載與原始碼

| 管道 | 連結 |
|------|------|
| 介紹頁 | [kk034kk034.github.io/cloakly](https://kk034kk034.github.io/cloakly/) |
| App Store | [Cloakly](https://apps.apple.com/tw/app/cloakly/id6814856725) |
| Google Play | [Cloakly](https://play.google.com/store/apps/details?id=com.cloud52.cloakly) |
| GitHub | [kk034kk034/cloakly](https://github.com/kk034kk034/cloakly) |

桌面版可自備金鑰、擷取系統聲音（Teams / Meet / Zoom 戴耳機開）；手機版適合人在現場、需要邊開邊記的時候。

若你也在補 RD → TPM 的能力地圖，可以一起看：[RD → TPM Roadmap](https://kk034kk034.github.io/tpm-roadmap/) —— Roadmap 練功，Cloakly 開工。

---

計畫可以靠 AI 加速；**會議鎖住的是人的時間**。  
Cloakly 想解的就是這一塊：專案脈絡裡的紀錄、行動、即時問答與檔案輔助。

有想法或 bug，歡迎到 [GitHub Issues](https://github.com/kk034kk034/cloakly/issues) 丟給我。
