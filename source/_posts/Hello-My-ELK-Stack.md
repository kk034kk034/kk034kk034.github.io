---
title: Hello, My ELK Stack
date: 2025-05-23 21:39:56
categories:
  - devops
tags:
  - log
---

# 前言

我不是維運工程師，但我希望自己開發的系統能夠：

1. 在用戶發現問題之前，先自己發現並處理  
2. 出錯時能快速排查，不靠 *.log 苦力 grep  
3. 平時能留下資料，未來好做統計與系統評估

這篇文章會分享我怎麼將開源工具整合進我的系統：
使用 **Filebeat、Logstash、Elasticsearch、Kibana**（簡稱 ELK），快速搭出一套後端 log 監控架構。
不靠複雜 infra，全程用 Docker Compose 一鍵啟動。
📦 完整 docker-compose.yml 放這裡 👉 [GitHub Repo](https://github.com/kk034kk034/management-system/blob/main/dev-compose.yml)

<!-- more -->

# Quick Start

## 架構概覽：Fastify + ELK Flow

```text
Fastify（logs/*.log）
  ↓
Filebeat：收集 log，JSON native 支援佳
  ↓
Logstash：欄位分類、格式轉換、前處理
  ↓
Elasticsearch：儲存與查詢，支援自動分日索引
  ↓
Kibana：查詢 & 圖表視覺化介面
```

## 事前準備：Fastify 寫入 JSON Log

我在 Fastify 專案中使用 Winston，把每次 API 請求都記錄成 JSON 格式的 log 檔案，方便後續被 ELK 收集與分析。

📁 log 檔案目錄範例如下：

```bash
/fastify_deploy/logs/
├── api-access.log        # 每筆 API 請求
``` 

📝 實際 log 範例（每行為一筆 JSON）：
``` log
[2025-05-23 08:15:24] INFO: {"timestamp":"2025-05-23T08:15:24.215Z","method":"POST","url":"/api/v1/auth/login","status":200,"duration":297,"user_id":"guest","ip":"172.18.0.1"}
```

## 設定整合：讓 Log 自動進入 ELK

所有服務都跑在 Docker 容器中，而 Fastify 寫出的 log 則透過 volume 掛載到 Filebeat。(如 Repo 所示之 dev-compose.yml)
並在啟用前，建立ELK所需設定檔。(如下所示)

📂 檔案結構（dev-compose.yml目錄下）：

``` bash
.
├── filebeat/
│   └── filebeat.yml # 輸出設定
├── logstash/
│   └── pipeline/
│       └── logstash.conf # 解析規則
``` 

📄 filebeat/filebeat.yml 範例：

```yaml
filebeat.inputs:
  - type: log
    paths:
      - /logs/*.log # 掛載 fastify log 的 volume 路徑

output.logstash:
  hosts: ["logstash:5044"] # 指向 logstash 容器
```

📄 logstash/pipeline/logstash.conf 範例：
```conf
input {
  beats {
    port => 5044
  }
}

filter {
  json {
    source => "message"
    skip_on_invalid_json => true
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "fastify-logs-%{+YYYY.MM.dd}"
  }
}
```

## 啟用
建立完上述資料夾與設定檔後，執行：
```bash
docker compose up -d
```

然後開啟瀏覽器進入：
```bash
http://localhost:5601
```

到 Kibana 建立 index pattern，例如：
```bash
fastify-logs-*
```

## 成果
🔍 查詢錯誤：type: api AND status: 500
🔎 篩選路徑：url: /api/v1/auth/login
📊 觀看圖表報表：
 - Top 5 最常被打的 API
 - status 401 的來源 IP 統計
 - 最近 30 分鐘錯誤數趨勢
