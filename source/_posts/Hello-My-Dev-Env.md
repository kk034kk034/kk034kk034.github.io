---
title: Hello, My Dev Env
date: 2025-02-23 16:21:14
categories:
	- build
tags:
	- env
---
# 前言
純軟的軟體工程師
通常大家拿到的電腦應該都是Windows居多
但開發環境還是使用linux比較順手的情況下
以下是我常用的環境與工具

# Quick Start
STEP 1:
搜尋: 開啟或關閉 Windows 功能
勾選: Windows 子系統 Linux 版(有Hyper-V的話也有勾選)
確定->等待->立即重新啟動
<div style="max-width: 500px;">
  {% asset_img image.png "圖片說明" %}
</div>

STEP 2: 
於 Microsoft Store 裡搜尋並安裝 Ubuntu
安裝完啟動，會要輸入(設定)帳號與密碼

STEP 3:
在系統管理員模式中開啟 PowerShell，輸入以下命令
```bash
wsl --install #通常目前下載就已經是WSL2了
wsl --set-default-version 2 #如果不是的話就將WSL預設版本調成WSL2
```

STEP 4:
安裝與設定Docker Desktop
<div style="max-width: 500px;">
  {% asset_img image-1.png "圖片說明" %}
</div>

STEP 5:
安裝與使用MobaXterm
<div style="max-width: 500px;">
  {% asset_img image-2.png "圖片說明" %}
</div>
<div style="max-width: 500px;">
  {% asset_img image-3.png "圖片說明" %}
</div>

STEP 6:
安裝與使用VS Code and/or Cursor
<div style="max-width: 500px;">
  {% asset_img image-4.png "圖片說明" %}
</div>
