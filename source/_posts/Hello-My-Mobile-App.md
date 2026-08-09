---
title: Hello, My Mobile App
date: 2026-08-09 14:06:00
categories:
  - mobile
tags:
  - android
  - ios
  - flutter
  - kotlin
  - swift
  - mvvm
  - clean-architecture
---

# 從 Java 到 Flutter：我的行動開發演進史

我從 Android 原生的 Java 寫起，之後轉到 Kotlin、又跨到 iOS 的 Swift，最後落腳在 Flutter。回頭看，這一路的每一次技術轉換，其實都不是為了追流行，而是**被某個具體的痛點推著走**的。這篇文章想把這條路重新走一遍，順便聊聊「跨平台 vs 原生」、「狀態管理」、「Clean Architecture」這些我在每個階段做過的取捨。

<!-- more -->

---

# 第 1 章｜起點：Android 原生 (Java)

<!-- TODO: 補充你當初怎麼入門 Android、當時的專案背景 -->

最初入門行動開發時，我是從 Android 原生 + Java 開始的。那個時期的寫法，現在回頭看很典型：一個 `Activity` 裡塞滿了 UI 控制、網路請求、資料處理的邏輯，變成一個什麼都管的「上帝類別（God Object）」。

**當時的痛點：**
- 程式碼高度耦合，改一個地方常常牽一髮動全身
- 幾乎無法做單元測試（邏輯和 UI 綁死）
- 檔案越長越難維護

<!-- TODO: 補充一個你當時真實遇到的例子（例如某個超長的 Activity） -->

這些痛點，就是推我走向下一步的動力。

---

# 第 2 章｜跟上官方：Kotlin + MVVM

Google 後來把 Kotlin 定為 Android 的首選語言，同時大力推廣 MVVM 架構，我也跟著轉。

**為什麼是 Kotlin？**
<!-- TODO: 補充你自己感受到的 Kotlin 好處，例如 null safety、語法簡潔、coroutine -->

**MVVM 解決了什麼？**

MVVM 把職責拆成三塊：

- **View**：只負責畫面與互動（Activity / Fragment）
- **ViewModel**：持有畫面需要的「狀態」與邏輯，不碰 UI 元件
- **Model**：資料來源（API、資料庫）

這裡其實就出現了「**狀態管理**」的雛形 —— 所謂狀態，就是 App 當下的資料（登入者是誰、清單是載入中還是成功）。ViewModel 的工作，就是「當狀態變了，通知 View 去更新」。

<!-- TODO: 可補一段 LiveData / StateFlow 的小心得或程式碼片段 -->

---

# 第 3 章｜跨到 iOS：Swift 也用 MVVM

因為專案通常都是雙平台一起出，我接著也寫了 iOS 原生的 Swift。為了讓兩邊的思維一致、好維護，我在 iOS 這邊一樣採用 MVVM 架構。

**雙平台一起維護的現實：**
- 同一個功能，邏輯幾乎要用兩種語言各寫一次
- 兩邊的行為細節還常常不一致，例如：
  - **返回鍵**：Android 有系統返回鍵/手勢，iOS 沒有，導航要各自處理
  - **權限流程**：iOS 拒絕後只能引導去設定開啟；Android 可再次詢問
  - **推播**：iOS 需要 APNs 憑證且使用者要主動同意；Android(FCM) 相對直接

<!-- TODO: 補充你玩過的深層連結 (deep link) / 背景任務在雙平台的差異心得 -->

寫到這裡，一個問題越來越明顯：**同樣的東西為什麼要維護兩套？** 這就是我轉向 Flutter 的伏筆。

---

# 第 4 章｜轉捩點：為什麼改用 Flutter

新專案開始後，我決定改用 Flutter。

**跨平台 vs 原生，我的取捨理由：**
- **人力**：一套 Dart 程式碼同時產出 iOS / Android，一個人顧雙平台的負擔直接減半（這是我最有感的一點）
- **上市速度**：雙平台可以同時上線
- **UI 一致性**：Flutter 自繪引擎，兩平台長得幾乎一模一樣

**那什麼情況我還是會選原生？**

當 App 需要重度存取硬體（相機、藍牙、感測器）、或要接「只有原生 SDK」的功能時，原生仍有優勢。不過即使用 Flutter，也能透過 **Platform Channel（平台通道）** 搭一座橋，讓 Dart 去呼叫原生的 Kotlin / Swift 程式碼。

<!-- TODO: 若有寫過 Platform Channel 的實例，補一段程式碼或情境 -->

**（順帶一提）RN 我還沒寫過**，之後希望有專案可以試試，再來比較它和 Flutter 的差異。

---

# 第 5 章｜Flutter 的工程化：Feature-First + Clean Architecture

到了 Flutter，我把架構認真地立起來：採 **Feature-First（以功能為單位）**，每個 feature 內部再用 **Clean Architecture** 分三層，依賴只能由外向內（UI → Domain → Data），反向禁止。

```
lib/
├─ core/                    # 共用工具與基礎設施（可跨專案複用）
│  ├─ error/                # 統一例外/失敗型別
│  ├─ network/              # API client、端點、連線偵測
│  ├─ utils/                # logger、日期/檔案/權限等 helper
│  └─ constants.dart        # 全域常數/資源路徑
│
├─ config/                  # 應用層設定（只屬於此 App）
│  ├─ di.dart               # 依賴注入（Composition Root）
│  └─ router.dart           # 路由表與導航守衛
│
├─ features/
│  ├─ ebook/                # 以「電子書」功能為例
│  │  ├─ presentation/      # 頁面、小元件、狀態(ViewModel/Bloc/Controller)
│  │  ├─ domain/            # entities、repository 介面、usecases（純邏輯）
│  │  └─ data/              # models、remote/local datasource、repository 實作
│  ...
│
└─ main.dart                # App 入口（啟動 DI、載入 Router、Theme）
```

**三層職責：**
- **presentation**：只負責顯示與互動，透過 UseCase 取資料，不直接打 API
- **domain**：純商業邏輯、可測試，不依賴 http / DB / 第三方框架
- **data**：把 API/DB 資料轉為 domain entities，處理錯誤、快取、重試

因為有了這套分層，我自然做到了：
- **API 串接的抽象層**（`core/network`）
- **統一錯誤處理**（`core/error`）
- **環境切換（dev / staging / prod）與金鑰管理**

<!-- TODO: 補充你環境切換 / 金鑰管理的實際做法（例如 --dart-define、.env、flavor） -->

**Flutter 的狀態管理選型：**

對照我以前在原生用的 MVVM，Flutter 常見的選項有：

| 方案 | 特色 | 適合 |
| --- | --- | --- |
| setState | 內建、最簡單 | 小畫面 |
| Provider | 官方推薦入門 | 中小型 |
| Riverpod | Provider 進化版，可測試 | 中大型（主流推薦） |
| Bloc | 事件驅動、架構嚴謹 | 大型、邏輯複雜 |
| GetX | 全包、上手快 | 快速開發 |

<!-- TODO: 補充你這個專案實際選了哪一種、為什麼 -->

---

# 第 6 章｜真實戰場的痛：Android 容易 Crash 與多尺寸排版

這一章是我最有感的部分。

**多尺寸 / 解析度的排版問題：**

Android 裝置的螢幕尺寸、解析度、長寬比五花八門，如果版面寫死（固定寬高、絕對座標），就會在某些機型上跑版，最後變成「同一個畫面要為好多尺寸各自維護」的惡夢。

我的處理方向：
<!-- TODO: 補充你實際用的做法，以下是常見手段，挑你有用到的展開 -->
- 用彈性佈局（`Expanded`、`Flexible`、`Wrap`）取代寫死尺寸
- 用 `MediaQuery` / `LayoutBuilder` 依螢幕大小做響應式調整
- 圖片與字級用相對比例，必要時做斷點（手機 / 平板）

**容易 Crash（穩定性）問題：**

比起 iOS，Android 更常遇到閃退，原因也更五花八門：機型/系統版本破碎化、記憶體不足被系統回收、權限或生命週期沒處理好、第三方 SDK 在特定裝置炸掉……而且往往是「使用者的某台手機」才會發生，本機開發時根本重現不出來。

我的處理方向：
<!-- TODO: 補充你實際遇到的 crash 情境與解法，以下是常見方向，挑你有用到的展開 -->
- **統一防護**：對可能拋錯的操作（網路、序列化、平台呼叫）集中做例外處理，避免未捕捉例外直接讓 App 掛掉
- **生命週期 / null 安全**：注意 Activity/Fragment 已銷毀後才回來的非同步結果、避免存取已釋放的資源
- **針對破碎化測試**：在多種機型 / 系統版本上驗證，而不是只測自己那一支
- **裝上崩潰監控**：像 Crashlytics / Sentry，能把「哪一行爆的、什麼機型、什麼系統版本」自動回報，不然只會收到使用者一句「你的 App 會當」（這點也呼應文末想補的維運主題）

---

# 小結

回頭看這條 Java → Kotlin MVVM → Swift MVVM → Flutter 的路，我最大的心得是：**架構不是為了漂亮，而是為了在下一次改動時少痛一點。** 每一次技術轉換，都是前一階段的痛點累積到臨界點後的自然結果。

接下來我想試試：
- 還沒碰過的 **React Native**，實際比較它和 Flutter
- 把手動上架的流程接上 **自動化 CI/CD**（Fastlane / Codemagic），以及上線後的**崩潰監控**

這些就留到下一篇 —— To be continued。

<!-- TODO: 若有對應的 GitHub 專案連結，補在這裡 -->
