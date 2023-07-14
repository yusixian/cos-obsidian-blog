---
title: Vercel Postgres 简明教程
link: vercel-postgres-tutorials
catalog: true
date: 2023-07-10 08:35:31
tags:
- 全栈
- 数据库
- postgreSql 
categories:
- 笔记   
---
%% #全栈 #数据库 #postgreSql  #%%
 
Vercel 作为一家提供开发、预览和部署网站的云平台，近期推出了自家的 Postgres 服务—— Vercel Postgres。这项服务的推出，让独立开发者和兴趣爱好者可以更方便地在 Vercel 平台上直接使用 Postgres 数据库，从而进一步提升开发效率。  

本文是根据 Vercel 官方的快速入门教程，提供一个简明的 Vercel Postgres 使用指南。我们将一步步地创建一个 Postgres 数据库，然后通过 Vercel 仪表板进行管理，最后使用 Vercel Postgres SDK 来填充数据库。

## 开始

在开始使用 Vercel Postgres 之前，我们需要满足一些前提条件。首先，你需要有一个已经存在的 Vercel 项目。其次，你需要安装 Vercel Postgres包和最新版本的 Vercel CLI。安装完成后，我们就可以开始创建我们的Postgres数据库了。

```bash
npm i @vercel/postgres
npm i -g vercel@latest
```

### 创建 Postgres 数据库
[Create a Postgres database](https://vercel.com/docs/storage/vercel-postgres/quickstart#create-a-postgres-database)
在 Vercel 已导入的项目中打开Storage -  Create 一个 Postgres 数据库
![[Pasted image 20230710204443.png]]
![[Pasted image 20230710204551.png]]

要创建新数据库，请在打开的对话框中执行以下操作：
1. 输入数据库名称，名称只能包含字母数字、`_`和 `-`，且不能超过 32 个字符。
2. 选择一个区域。建议选择地理位置靠近边缘和无服务器功能区域的区域，以减少延迟（这里直接选了默认）
3. 单击创建

![[Pasted image 20230710204642.png]]
4. 设置环境、环境变量前缀
![[Pasted image 20230710204859.png]]


### 查看创建的内容

[Review what was created](https://vercel.com/docs/storage/vercel-postgres/quickstart#review-what-was-created)

您的空数据库是在指定的区域中创建的。

在项目中创建了 Postgres 数据库后，Vercel 会自动创建以下环境变量并将其添加到项目中。接下来要将把它们拉到本地，以便我们可以在项目中使用它们。

- `POSTGRES_URL`
- `POSTGRES_PRISMA_URL`
- `POSTGRES_URL_NON_POOLING`
- `POSTGRES_USER`
- `POSTGRES_HOST`
- `POSTGRES_PASSWORD`
- `POSTGRES_DATABASE`

在Setting - Environment Variables 中可以查看这些环境变量

![[Pasted image 20230710205411.png]]

关于 Vercel CLI 的使用，见：[Vercel CLI Overview | CLI Commands | Vercel Docs](https://vercel.com/docs/cli)

> Vercel CLI 要求您在访问特定于用户的内容或执行管理任务之前登录并进行身份验证。在终端环境下，可以使用 `vercel login` ，需要手动输入。在无法手动输入的 CI/CD 环境中，您可以在令牌页面上创建令牌，然后使用 `--token` 选项进行身份验证。

使用 [vercel link](https://vercel.com/docs/cli/link) 关联本地和 Vercel 项目，交互式的选好组织、项目等拉取环境变量。

```bash
vercel link
vercel env pull .env.development.local
```

![[Pasted image 20230710210404.png]]

接下来通过 Prisma 连接到 Vercel Postgres：[How to Build a Fullstack App with Next.js, Prisma, & PostgreSQL](https://vercel.com/guides/nextjs-prisma-postgres)。
```bash
npm install prisma --save-dev
npx prisma init # 创建 prisma 文件夹及.env 可以将.env删了，因为已经拉取了.env.development.local
```

替换 schema.prisma 文件内容为

```bash
// schema.prisma

generator client {
  provider = "prisma-client-js"
  previewFeatures = ["jsonProtocol"]
}

datasource db {
  provider = "postgresql"
  url = env("POSTGRES_PRISMA_URL") // uses connection pooling
  directUrl = env("POSTGRES_URL_NON_POOLING") // uses a direct connection
  shadowDatabaseUrl = env("POSTGRES_URL_NON_POOLING") // used for migrations
}

model Post {
  id        String     @default(cuid()) @id
  title     String
  content   String?
  published Boolean @default(false)
  author    User?   @relation(fields: [authorId], references: [id])
  authorId  String?
}

model User {
  id            String       @default(cuid()) @id
  name          String?
  email         String?   @unique
  createdAt     DateTime  @default(now()) @map(name: "created_at")
  updatedAt     DateTime  @updatedAt @map(name: "updated_at")
  posts         Post[]
  @@map(name: "users")
}
```

此时文件结构大致如下
![[Pasted image 20230710211136.png]]

要在数据库中实际创建这些表，使用 Prisma CLI 的以下命令：

```
npx prisma db push
```

根据您的 Prisma 架构在数据库中创建表。

您应该看到以下输出：

```
Your database is now in sync with your schema. Done in 2.10s
```

将 Prisma 架构推送到数据库的输出。
恭喜，表已创建！继续使用 Prisma Studio 添加一些初始虚拟数据。运行以下命令：

```
1npx prisma studio
```