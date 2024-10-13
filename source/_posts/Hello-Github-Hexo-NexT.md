---
title: Hello, Github-Hexo-NexT
date: 2024-07-25 11:31:52
categories:
	- build
tags:
	- blog
---
# Needs
==Github Page + Hexo框架 + NexT主題==
- Free
- Own Blog
- Easy Use
- Flexibility

# Quick Start
## Github Page
1. 先有一個Github帳號
2. 去新增專案(New repository)
3. repository name必填為"your_github_name.github.io"
4. 設定完成後，進入"https://your_github_name.github.io/" 頁面就是屬於你的網頁了！
## Hexo框架
1. windows系統的電腦直接安裝[Node.js](https://nodejs.org/en)
2. 就可以cmd下
```bash
npm install -g hexo-cli # 安裝hexo框架
hexo init blog #在當前目錄建立一個blog資料夾
```
3. 把blog裡面的檔案全部複製到你剛建的git local repo
4. 就可以cmd下
```bash
npm install #根據"package.json"自動安裝需要的套件
```
5. 為了快速先看到有東西，必須先對"_config.yml"其中三部分做基本修改，以下為我的範例config
	5-1. # Site
	```
	title: Kate's Blog
	subtitle: 'subtitle'
	description: 'description'
	keywords:
	author: Kate Cheng
	language: zh-TW
	timezone: 'Asia/Taipei'
	```
	5-2. # URL
	```
	## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
	url: https://kk034kk034.github.io/
	root: /
	permalink: :year/:month/:day/:title/
	permalink_defaults:
	pretty_urls:
	  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
	  trailing_html: true # Set to false to remove trailing '.html' from permalinks
	```
	5-3. # Deployment
	```
	## Docs: https://hexo.io/docs/one-command-deployment
	deploy:
	  type: git
	  repo: https://github.com/kk034kk034/kk034kk034.github.io.git
	  branch: main
	  ```
6. 就可以cmd下以下指令部屬看結果~
```bash
hexo clean #將目前資料夾中的所有靜態網頁清除
hexo generate #或hexo g，
hexo server #會部屬在本地(127.0.0.1:4000)，用來確認當前設計
hexo deploy #或hexo d，將本地靜態網頁自動部屬到Git Page，所以5-3的部分要設定正確
```
## NexT主題
1. 尋找自己喜歡的[Hexo Themes](https://hexo.io/themes/?source=post_page-----4d295ed96236--------------------------------)
2. 我最後是裝[NexT主題](https://github.com/next-theme/hexo-theme-next)
```bash
git clone https://github.com/next-theme/hexo-theme-next themes/next
```
3. 並修改"_config.yml"的```theme: next```
4. 接下來就都是"themes/next/_config.yml"的事了，以下為我的範例config
	4-1. # Schemes
	```
	scheme: Gemini
	```
	4-2. # Dark Mode
	```
	darkmode: true
	```
	4-3. # Menu Settings
	```
	menu:
	  home: / || home
	  about: /about/ || user
	  tags: /tags/ || tags
	  categories: /categories/ || th
	  archives: /archives/ || archive
	```
	4-4. # Sidebar Avatar
	```
	avatar:
	  # Replace the default image and set the url here.
	  url: /images/GGC02414-1.jpg
	  # If true, the avatar will be displayed in circle.
	  rounded: false
	  # If true, the avatar will be rotated with the cursor.
	  rotated: false
	```
	4-5. # Social Links
	```
	social:
	  GitHub: https://github.com/kk034kk034 || fab fa-github
	  LinkedIn: https://www.linkedin.com/in/kate-cheng-a8b94a189/ || fab fa-linkedin
	```
5. 其中，about、tags、categories，要用hexo指令額外建Page點進去才會有東西
	5-1. 增加"關於"
	```bash
	hexo new page about
	```
	接著修改剛剛產生的 source/about/inex.md 成下面的樣子並儲存。
	```
	---
	title: about
	date: 2024-07-25 01:34:24
	type: "page"
	---
	```
	5-2. 增加"標籤"
	```bash
	hexo new page tags
	```
	接著修改剛剛產生的 source/tags/inex.md 成下面的樣子並儲存。
	```
	---
	title: tags
	date: 2024-07-25 04:02:34
	type: "tags"
	---
	```
	5-3. 增加"分類"
	```bash
	hexo new page categories
	```
	接著修改剛剛產生的 source/categories/inex.md 成下面的樣子並儲存。
	```
	---
	title: categories
	date: 2024-07-25 04:01:49
	type: "categories"
	---
	```
6. 最後就是可以開始寫文章以及使用分類、標籤功能啦
```bash
hexo new post "Hello, Github-Hexo-NexT"
```
接著在剛剛產生的 source/_posts/Hello-Github-Hexo-NexT.md 頭部增加"categories:"與"tags:"範例如下，就可以開始用MarkDown語法寫文章啦!
```
---
title: Hello, Github-Hexo-NexT
date: 2024-07-25 11:31:52
categories:
	- build
tags:
	- blog
---
