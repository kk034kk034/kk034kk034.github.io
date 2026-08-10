---
title: Hello, My GitHub Flow
date: 2025-05-24 21:18:12
categories:
  - vcs
tags:
  - github
  - workflow
---

# 如何在社群上和大神合作?

> 我自己在寫 side project 或是參與多人協作時，發現了 GitHub Flow 的重要性。它讓我在處理分支、同步進度、合併改動時比較不容易踩雷。

誠心推薦的教學影片：[十分钟学会正确的 GitHub 工作流](https://www.youtube.com/watch?v=uj8hjLyEBmU)

<!-- more -->

## 前言

GitHub Flow 是一套非常實用的流程：

1. 為每個功能建立分支
2. 本地提交並推送到 GitHub
3. 與 main 分支保持同步（rebase）
4. 發起 Pull Request，進行 code review
5. 使用 squash and merge 合併
6. 刪除分支，保持倉庫整潔

## 步驟一: 建立功能分支

```bash
git checkout -b feature/awesome-update
```

在 main 上開發是禁忌。先從 main 切出分支再動手，不管是新增功能還是修 Bug，都要獨立開發、獨立測試，這樣出問題才不會拖垮整個專案。

## 步驟二: 編輯、提交、檢查差異

修改完代碼之後，記得：

```bash
git diff
git add .
git commit -m "feat: add awesome update"
```

養成 good commit message 的習慣，後面回頭看的時候會感謝自己。建議在提交前用 `git diff` 看一下，確保不小心加了 debug 或多餘的修改。

## 步驟三: 與 main 同步，保持乾淨提交歷史

功能開發不會在真空中進行。main 分支的進度可能已經更新，我們要透過 rebase 整合最新的變更：

```bash
git fetch origin
git rebase origin/main
```

> rebase 會讓提交記錄保持線性，是 GitHub Flow 推薦的做法。若出現衝突，就手動解、`git rebase --continue`。

## 步驟四: 發起 Pull Request（PR）

當功能開發完成後，推上 GitHub：

```bash
git push origin feature/awesome-update
```

進到 GitHub 倉庫，發起 Pull Request，指明要合併到 `main`。在 PR 裡可以：

- tag reviewer（協作者）
- 解釋變更動機與內容
- 附上截圖或測試方式

## 步驟五: 使用 Squash and Merge 合併

PR 通過後，請選擇 **Squash and Merge** 而不是 Merge commit：

✅ 優點：

- 合併進 main 時只留下 1 筆 commit，保持簡潔歷史
- 雜亂的小 commit 不會污染主線

合併後記得：

```bash
git branch -d feature/awesome-update
git push origin --delete feature/awesome-update
```
