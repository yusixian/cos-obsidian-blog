---
title: Nest 学习记录（一） 了解安装与使用
link: nest-learning-1
catalog: true
date: 2023-06-16 11:43:02
tags:
- nest
- 前端
- 后端
- node
categories:
- [笔记, 前端]
---
%% #nest  #前端 #后端 #node  %%

## 介绍

[Nest.js](https://nestjs.com/) 是一个用于构建高效、可扩展的 Node.js 服务器端应用程序的框架。它使用渐进式 JavaScript，用 TypeScript 构建，并结合了面向对象编程（OOP）、函数式编程（FP）和函数响应式编程（FRP）的元素。

Nest.js 提供了一个开箱即用的应用程序架构，允许开发者和团队创建高度可测试、可扩展、松散耦合且易于维护的应用程序。这种架构深受 Angular 的启发，使得 Nest.js 不仅提供了一种组织后端应用程序的强大方式，同时也提供了一个跨前后端的统一的开发体验。

在底层，Nest.js 使用了如 Express（默认）这样的强大 HTTP 服务器框架，并可以选择配置为使用 Fastify！Nest.js 在这些常见的 Node.js 框架（Express/Fastify）之上提供了一个抽象层，但也直接将它们的 API 暴露给开发者。这给了开发者使用第三方模块的自由，这些模块可用于底层平台。

## 安装

安装 Nest.js 非常简单。你可以使用 Nest CLI 来搭建项目，或者克隆一个启动项目（两种方式都会产生相同的结果）。

### 使用 Nest CLI 搭建项目

运行以下命令。这将创建一个新的项目目录，并用初始的核心 Nest 文件和支持模块填充目录，为你的项目创建一个常规的基础结构。对于首次使用的用户，我们推荐使用 Nest CLI 创建新项目。

```bash
npm i -g @nestjs/cli
nest new project-name
```

打开浏览器并导航到 http://localhost:3000/。

你也可以通过 npm（或 yarn）手动创建一个新项目，安装核心和支持文件。在这种情况下，你需要自己创建项目的样板文件。

```bash
npm i --save @nestjs/core @nestjs/common rxjs reflect-metadata
```

## 了解Nest.js的功能

在Nest.js中，模块是一个使用 `@Module()` 装饰器注解的类。 `@Module()` 装饰器提供了元数据，Nest.js 用这些元数据来组织应用程序的结构。

每个应用程序至少有一个模块，即**根模块**。根模块是 Nest.js 用来构建应用程序图形的起点，应用程序图形是Nest.js用来解析模块和提供者关系及依赖的内部数据结构。虽然理论上非常小的应用程序可能只有根模块，但这并不是典型的情况。我们想强调的是，模块是作为有效组织组件的强烈推荐的方式。因此，对于大多数应用程序，结果架构将使用多个模块，每个模块封装一组紧密相关的功能。

`@Module()` 装饰器接受一个对象，其属性描述了模块：
- `providers`：将由Nest注入器实例化的提供者，这些提供者至少可以在此模块中共享。
- `controllers`：在此模块中定义的一组控制器，必须实例化这些控制器。
- `imports`：导入的模块列表，这些模块导出了此模块所需的提供者。
- `exports`：由此模块提供并应在导入此模块的其他模块中可用的提供者的子集。你可以使用提供者本身或仅使用其令牌（提供值）。

模块默认封装提供者。这意味着 无法注入既不直接属于当前模块又不从导入的模块中导出的提供者 。因此，你可能会将模块的导出提供者视为模块的公共接口或API。

## 在项目中使用Nest.js

在Nest.js中，你可以使用Nest CLI来创建新项目。首先，你需要确保你的操作系统上已经安装了Node.js（版本>=16）。然后，你可以运行以下命令来创建一个新的Nest项目：

```bash
npm i -g @nestjs/cli
nest new project-name
```

这将创建一个新的项目目录，并用初始的核心Nest文件和支持模块填充目录，为你的项目创建一个常规的基础结构。

在创建的项目中，你会看到以下几个核心文件：

- `app.controller.ts`：一个基本的控制器，带有一个路由。
- `app.controller.spec.ts`：控制器的单元测试。
- `app.module.ts`：应用程序的根模块。
- `app.service.ts`：带有一个方法的基本服务。
- `main.ts`：应用程序的入口文件，使用NestFactory来创建一个Nest应用程序实例。

在`main.ts`中，我们使用NestFactory的核心函数来创建一个Nest应用程序实例。NestFactory暴露了一些静态方法，允许创建一个应用程序实例。`create()`方法返回一个应用程序对象，这个对象提供了一组方法，这些方法将在接下来的章节中描述。

```javascript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
 const app = await NestFactory.create(AppModule);
 await app.listen(3000);
}

bootstrap();
```

一旦安装过程完成，你可以运行以下命令来启动应用程序，监听入站HTTP请求：

```bash
npm run start
```

这个命令将启动应用程序，HTTP服务器将在`src/main.ts`文件中定义的端口上监听。一旦应用程序运行起来，打开你的浏览器并导航到`http://localhost:3000/`。你应该会看到`Hello World!`的消息。

你也可以运行以下命令来启动应用程序，这个命令将监视你的文件，自动重新编译和重新加载服务器：

```bash
npm run start:dev
```
