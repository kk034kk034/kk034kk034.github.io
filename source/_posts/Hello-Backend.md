---
title: Hello, Backend
date: 2025-05-03 18:06:12
categories:
  - backend
tags:
  - rbac
  - fastify
  
---

# 使用 Fastify、Sequelize 和 JWT 實現 RBAC 權限管理

在這篇文章中，我將分享如何使用 Fastify、Sequelize 和 JWT 搭建一個後端架構，並且通過 RBAC（基於角色的訪問控制） 來管理用戶的訪問權限。這些技術的組合讓我們能夠快速且安全地處理用戶身份認證與角色管理，並確保 API 的安全性。



# 為何選擇這些技術？

- Fastify：這是一個高效且易於擴展的 Node.js 框架，提供了強大的性能和極為簡單的 API 設計，能夠輕鬆應對高並發的請求。

- Sequelize：作為一個強大的 ORM 工具，Sequelize 可以簡化與資料庫的交互，並且支持多種關聯型資料庫，適合用來處理用戶資料和角色資料。

- JWT：JWT 是一種輕量級的身份驗證機制，可以在不同服務之間安全地傳遞用戶的身份信息，並且能夠實現無狀態的認證系統。


# 如何實現 RBAC（基於角色的訪問控制）

1. 設定資料模型
首先，我們需要設計 User 和 Role 模型，並使用 Sequelize 來管理這些資料。每個用戶都會被分配一個角色，角色則定義了用戶可以訪問的資源。
2. 設定角色和權限
我們使用 AccessControl 库來設定每個角色可以執行的操作。例如，groupViewer 角色只能讀取資料，而 groupManager 則擁有更多的操作權限。
3. 中間件：基於角色的授權
為了保證每個用戶只能訪問他們被授予權限的資源，我們在 Fastify 中創建了一個名為 authorize 的中間件，來進行每次請求的授權檢查。
4. 設定 JWT 認證
用戶登錄後，系統會發送一個 JWT 令牌，這個令牌會包含用戶的角色信息。在後續的 API 請求中，我們會驗證 JWT，並根據角色來授權。


# 使用 JWT 和 RBAC 進行授權操作
一旦用戶成功登錄並獲得 JWT，後端可以根據用戶角色來授權操作。例如，讓 groupManager 角色的用戶將某個用戶分配到某個組織並賦予相應的角色。

分配用戶到某個組織並賦予角色：
在這個 API 中，我們會驗證用戶的角色，然後根據角色授予其相應的權限。假設我們要實現將用戶分配到某個組織並賦予角色的功能，這樣的 API 需要確保只有 groupManager 和 groupAdmin 角色能夠進行此操作。

```js
fastify.post('/assign-user-role', { preValidation: [fastify.authorize('createAny', 'userRole')] }, async (request, reply) => {
  const { userId, organizationId, roleId } = request.body;

  // 確認用戶是否已經存在
  const user = await User.findByPk(userId);
  if (!user) {
    return reply.code(404).send({ error: 'User not found' });
  }

  // 分配角色
  const userRole = await UserRole.create({
    user_id: userId,
    organization_id: organizationId,
    role_id: roleId
  });

  return reply.code(200).send({ message: 'User role assigned successfully', userRole });
});
```

# 小結
這篇文章介紹了如何使用 Fastify、Sequelize 和 JWT 實現一個帶有 RBAC（基於角色的訪問控制）的後端架構。通過簡單的程式範例，我們展示了如何設定角色和權限，並且使用 JWT 來進行用戶身份驗證和授權操作。這個架構簡單且高效，適合用於需要角色管理和精細化權限控制的應用。

如果你有興趣查看具體的程式碼實現，可以前往我的 GitHub 相關專案：https://github.com/kk034kk034/management-system
