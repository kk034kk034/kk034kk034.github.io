# kk034kk034.github.io

Kate's Blog，使用 **Hexo + NexT 主題**，部署於 GitHub Pages。

## 分支架構

本 repo 用兩個分支分別存放「原始碼」與「部署輸出」，互不干擾：

| 分支 | 內容 | 用途 |
| --- | --- | --- |
| `source` | 原始碼（`source/`、`_config.yml`、`_config.next.yml` 等） | 平常寫作與版控備份 |
| `main` | Hexo 產生的靜態網站 | GitHub Pages 對外服務（由 `hexo d` 自動推送） |

> 不進版控的項目：`node_modules/`、`db.json`、`public/`、`themes/next/`（皆為套件、建置產物或外部主題，見 `.gitignore`）。
> 主題的個人化設定改放在版控範圍內的 `_config.next.yml`，換電腦不會遺失。

## 換一台電腦接手

```bash
# 1. 拉原始碼（指定 source 分支）
git clone -b source https://github.com/kk034kk034/kk034kk034.github.io.git
cd kk034kk034.github.io

# 2. 安裝套件
npm install

# 3. 重新取得 NexT 主題（未進版控）
git clone https://github.com/next-theme/hexo-theme-next themes/next
# 主題的個人化設定已在 _config.next.yml，會自動套用
```

## 日常流程

```bash
# 預覽
hexo clean
hexo generate   # 或 hexo g
hexo server     # 或 hexo s，本地預覽 http://127.0.0.1:4000

# 備份原始碼（在 source 分支）
git add source/_posts/your-post.md
git commit -m "Add post: ..."
git push

# 發布上線（自動推送到 main）
hexo deploy     # 或 hexo d
```

## 參考資料

- [建立一個屬於自己的「程式」部落格（Hexo + NexT）](https://minglun-wu.medium.com/%E5%BB%BA%E7%AB%8B%E4%B8%80%E5%80%8B%E5%B1%AC%E6%96%BC%E8%87%AA%E5%B7%B1%E7%9A%84-%E7%A8%8B%E5%BC%8F-%E9%83%A8%E8%90%BD%E6%A0%BC-4d295ed96236)
- [使用 Github Page 製作個人網站（Hexo / NexT）](https://allen108108.github.io/blog/2019/10/07/%E4%BD%BF%E7%94%A8%20Github%20page%20%E8%A3%BD%E4%BD%9C%E5%80%8B%E4%BA%BA%E7%B6%B2%E7%AB%99%20%E2%80%93%20%E4%BB%A5%20Hexo%20_%20Next%20%E8%A3%BD%E4%BD%9C%E9%83%A8%E8%90%BD%E6%A0%BC/)

## 曾嘗試過的主題

- [Academia](https://github.com/PhosphorW/hexo-theme-academia?tab=readme-ov-file)
- [NexT (dawn)](https://github.com/Ruffianjiang/hexo-theme-dawn?tab=readme-ov-file)
- [WebStack](https://github.com/HCLonely/hexo-theme-webstack)
