---
title: Hello, Gitea-Action-and-CI
date: 2025-06-17 22:27:34
categories:
  - devops
tags:
  - cicd
  - gitea
  - docker
---

# 用 Gitea Actions 打造你的 CI/CD 自動化流程 🚀

> 這篇文章將帶你一步步用 Gitea Actions 打造自己的自動部署流程，從安裝 Runner 到實作 workflow，不只是跑測試，還能部署 Docker 應用。


## 為什麼選 Gitea Actions？🤔

我選擇它的幾個原因：
- ✅ 自架、開源、輕量，不依賴 GitHub.com
- ✅ 支援 GitHub Actions 的語法，相容度高
- ✅ 跟 Gitea repository 整合超順，原生支援 PR trigger
- ✅ 社群活躍，仍持續更新（2025 年開始加入 matrix 策略等進階特性）


## 前置準備 🧰

### 1️⃣ 安裝 Gitea Actions Runner

```bash
curl -s https://dl.gitea.com/act_runner/latest | bash
chmod +x act_runner
./act_runner register
```

註冊過程會問你 Gitea server 的網址、Access Token、repository 等資訊。完成後會自動產生 ~/.act_runner/ 配置檔，然後就能啟動 runner：

```bash
./act_runner daemon &
```

📌 避坑提醒：

 - Token 必須有 actions:write 權限

 - 如果你是 root 使用者安裝，請留意目錄權限問題，建議建立專用 ciuser

## 設定 CI/CD 工作流程（workflow）
在你的 repository 裡建立 .gitea/workflows/ci.yml：

```yaml
name: Build and Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Run Unit Test
        run: |
          npm install
          npm test

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy via Docker Compose
        run: |
          docker compose -f docker-compose.prod.yml up -d --build
```

✅ 這段流程做了三件事：
1. Pull 你的程式碼
2. 安裝與執行測試
3. 測試通過後，自動部署 Docker 版本到正式機


## 配合 .env 與 Volume 管理你的部署設定
我會這樣設計 docker-compose.prod.yml：

```yaml
services:
  web:
    build: .
    image: myapp:latest
    env_file: .env.prod
    ports:
      - "80:3000"
    volumes:
      - ./data:/app/data
```

📌 實務建議：
 - 使用 .env.prod 分離環境參數
 - 將部署流程分開維護（ci.yml 不要直接包含敏感參數）

## 實戰部署與整合體驗
我把這套流程整合進我自己的專案後，每次 push 到 main 分支，就會：
 - 自動執行測試
 - 成功後觸發部署
 - 若失敗則不更新正式環境（避免髒 build）

🚨 避坑提醒：
 - 若你在 deploy 階段用了 docker compose down && up，要確保 volume 資料別被砍掉！
 - 測試與正式機若共用 Runner，記得使用 --project-name 隔離工作目錄
