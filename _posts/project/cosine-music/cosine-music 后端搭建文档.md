## 初始化

### prisma & express & ts

```bash
npm init -y
npm i prisma typescript ts-node-dev @types/node @types/express  -D
npm i @prisma/client express
npx prisma init
```

- [QuickStart With TypeScript And Mysql](https://www.prisma.io/docs/getting-started/quickstart)
- [Ts + express 后端项目搭建记录](https://juejin.cn/post/7069770431871320078#heading-1)

package.json 新增启动脚本

```json
"scripts": {
    "dev": "ts-node-dev --respawn --transpile-only src/app.ts"
},
```

新增 tsconfig.json

```json
{
  "compilerOptions": {
    "outDir": "build",
    "target": "es5",
    "module": "commonjs",
    "sourceMap": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  }
}
```

新增 app.ts 和 routes

```
├─ .gitignore
├─ .prettierrc.js
├─ package-lock.json
├─ package.json
├─ prisma
│  └─ schema.prisma
├─ README.md
├─ src
│  ├─ app.ts
│  └─ routes
│     └─ index.ts
├─ tsconfig.json
└─ .env
```

```typescript
// src/app.ts

import express from 'express';
import routes from './routes'; // 路由

const app = express();

app.use(express.json());

const PORT = 1337;

// 启动
app.listen(PORT, async () => {
  console.log(`App is running at http://localhost:${PORT}`);
  routes(app);
});
```

```typescript
// src/routes/index.ts

import { Express, Request, Response, Router } from 'express';

// 路由配置接口
interface RouterConf {
  path: string;
  router: Router;
  meta?: any;
}

// 路由配置
const routerConf: Array<RouterConf> = [];

function routes(app: Express) {
  // 根目录
  app.get('/', (req: Request, res: Response) => res.status(200).send('Hello Shinp!!!'));

  routerConf.forEach((conf) => app.use(conf.path, conf.router));
}

export default routes;
```

### 代码规范

```bash
npm i -D prettier eslint eslint-plugin-prettier @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-prettier
```

.eslintrc.js

```javascript
// .eslintrc.js

module.exports = {
  root: true,
  // 扩展规则
  extends: ['eslint:recommended', 'plugin:@typescript-eslint/recommended', 'plugin:prettier/recommended', 'prettier'],
  parserOptions: {
    ecmaVersion: 12,
    parser: '@typescript-eslint/parser',
    sourceType: 'module',
  },
  // 注册插件
  plugins: ['@typescript-eslint', 'prettier'],
  // 规则 根据自己需要增加
  rules: {
    'no-var': 'error',
    'no-undef': 0,
    '@typescript-eslint/consistent-type-definitions': ['error', 'interface'],
  },
};
```

.prettierrc.js 配置

```javascript
// .prettierrc.js
module.exports = {
  tabWidth: 2,
  useTabs: false,
  semi: true,
  singleQuote: true,
  quoteProps: 'as-needed',
  jsxSingleQuote: false,
  trailingComma: 'all',
  bracketSpacing: true,
  jsxBracketSameLine: false,
  arrowParens: 'always',
  rangeStart: 0,
  rangeEnd: Infinity,
  requirePragma: false,
  insertPragma: false,
  proseWrap: 'preserve',
  htmlWhitespaceSensitivity: 'css',
  vueIndentScriptAndStyle: false,
  endOfLine: 'lf',
  embeddedLanguageFormatting: 'auto',
  printWidth: 128,
};
```

再往下基本都是这个里面的了，然后进行拓展

- [Ts + express 后端项目搭建记录](https://juejin.cn/post/7069770431871320078#heading-1)

## 搭建用户认证系统

首先安装 `bcryptjs` 和 `jsonwebtoken` ，用于密码散列和生成令牌。

```bash
yarn add bcryptjs jsonwebtoken
```

### bcryptjs

在 `src/middleware/user.middleware.ts` 中编写加密密码的中间件

```typescript
/**
 * @description: 密码加密
 */
export const cryptPassword = (req: RequestBody<Prisma.UserCreateInput>, res: Response, next: NextFunction) => {
  const { password } = req.body;
  const salt = bcrypt.genSaltSync(10);
  // hash保存的是密文
  const hash = bcrypt.hashSync(password, salt);
  req.body.password = hash;
  next();
};
```

顺便把验证唯一用户的中间件也加上

```typescript
/**
 * @description: 验证注册用户唯一性
 */
export const validateUniqueUser = async (req: RequestBody<Prisma.UserCreateInput>, res: Response, next: NextFunction) => {
  const { user_name } = req.body;
  const user = await userService.findUserByUserName(user_name);
  if (user) commonRes.error(res, null, '用户名已存在');
  else next();
};
```

现在存入数据库的密码便是经过加密的啦

## 嵌入 apifox 在线接口文档等

使用 [Express - art-template](https://aui.github.io/art-template/express/)

首先安装

```bash
yarn add art-template express-art-template
```

在 `express` 中，有一个 `render()` 方法，一般情况下是不能用的，但配合这个模板引擎，就可以使用了

- 用法：`render("文件名",{模板数据});`
- 在 src 下新建 views 文件夹存放我们的视图

```html
<!-- src/views/index.html -->
<!DOCTYPE html> 
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>index</title>
  </head>
  <body>
    <h1>{{name}}</h1>
    <p>恭喜~ 服务成功跑起来啦</p>
    <p>
      接口文档 👉 <a href="https://apifox.com/apidoc/shared-6af432f7-e747-42a0-b64d-058b9b288df0/api-70897715">📖接口文档</a>
    </p>
    <iframe
      src="https://apifox.com/apidoc/shared-6af432f7-e747-42a0-b64d-058b9b288df0/api-70897715"
      width="100%"
      height="900px"
    ></iframe>
  </body>
</html>
```

```typescript
// src/app.ts
app.engine('html', require('express-art-template'));
app.set('view options', {
  debug: process.env.NODE_ENV !== 'production',
});
app.set('views', path.join(__dirname, 'views'));
app.get('/', function (req, res) {
  res.render('index.html', {
    //模板数据如下
    name: '首页',
  });
});
```

此时就出来了

![[Pasted image 20230415033024.png]]

## CORS 解决跨域

```bash
yarn add cors
```

## 设计数据库结构

首先，列出可能需要的表和它们的字段：

### 用户表 User

| 字段名称  | 数据类型 | 长度 | 字段描述     | 其他说明                   |
| --------- | -------- | ---- | ------------ | -------------------------- |
| id        | int      |      | 主键id       |                            |
| user_name | varchar  | 255  | 用户名       | 登录用户名，唯一           |
| password  | varchar  | 255  | 登录密码     |                            |
| type      | int      |      | 用户类型     | 0为普通用户，1为管理员用户 |
| status    | int      |      | 用户状态     | 0为正常用户，1为被封禁用户 |
| avatar    | varchar  | 255  | 用户头像     | 头像图片路径               |
| city      | varchar  | 255  | 用户所在城市 |                            |
| sex       | int      |      | 用户性别     | 0为未知，1为女，2为男      |
| email     | varchar  | 255  | 用户邮箱     |                            |
| name      | varchar  | 255  | 昵称         |                            |
| createdAt | datetime | 3    | 用户创建时间 | Unix时间戳                 |
| updatedAt | datetime | 3    | 记录更新时间 | Unix时间戳                 |

### 音乐表 Music

| 字段名称      | 数据类型 | 长度 | 字段描述     | 其他说明                                        |
| ------------- | -------- | ---- | ------------ | ----------------------------------------------- |
| id            | int      |      | 音乐id       | 主键                                            |
| title         | varchar  | 255  | 音乐标题     |                                                 |
| coverUrl      | varchar  | 255  | 封面图片路径 |                                                 |
| url           | varchar  | 255  | 歌曲资源路径 | mp4文件                                         |
| playCount     | bigint   |      | 播放量       |                                                 |
| artist        | varchar  | 255  | 歌手名称     | 可能有不在该平台的歌手，直接写名字              |
| artistId      | int      |      | 歌手id       | 若歌手在该平台，该字段为user_id                 |
| lyric         | varchar  | 255  | lrc格式歌词  |                                                 |
| lyricAuthorId | int      |      | 歌词贡献者id | 外键                                            |
| status        | int      |      | 音乐状态     | 0为刚发布尚未审核，1为审核通过状态，2为封禁状态 |
| createdAt     | datetime | 3    | 记录创建时间 | Unix时间戳                                      |
| updatedAt     | datetime | 3    | 记录更新时间 | Unix时间戳                                      |
| deletedAt     | datetime | 3    | 软删除时间   | Unix时间戳                                      |
| categoryId    | int      |      | 所属分区id   | 外键                                            |

### 歌单表 Playlist

| 字段名称    | 数据类型 | 长度 | 字段描述       | 其他说明   |
| ----------- | -------- | ---- | -------------- | ---------- |
| id          | int      |      | 歌单id         | 主键       |
| title       | varchar  | 255  | 歌单标题       |            |
| description | varchar  | 255  | 歌单描述       |            |
| coverUrl    | varchar  | 255  | 封面图片路径   |            |
| creator     | int      |      | 该歌单创建者id | 外键       |
| createdAt   | datetime | 3    | 创建时间       | Unix时间戳 |
| updatedAt   | datetime | 3    | 记录更新时间   | Unix时间戳 |
| deletedAt   | datetime | 3    | 软删除时间     | Unix时间戳 |

### 标签表 Tag

| 字段名称  | 数据类型 | 长度 | 字段描述      | 其他说明   |
| --------- | -------- | ---- | ------------- | ---------- |
| id        | int      |      | 标签id        | 主键       |
| name      | varchar  | 255  | 标签名        |            |
| icon      | varchar  | 255  | icon名称/句柄 | 可为空     |
| createdAt | datetime | 3    | 创建时间      | Unix时间戳 |
| updatedAt | datetime | 3    | 记录更新时间  | Unix时间戳 |

### 分区表 Category

| 字段名称    | 数据类型 | 长度 | 字段描述     | 其他说明   |
| ----------- | -------- | ---- | ------------ | ---------- |
| id          | int      |      | 分区id       | 主键       |
| name        | varchar  | 255  | 分区名称     |            |
| description | varchar  | 255  | 分区描述     |            |
| coverUrl    | varchar  | 255  | 分区图片路径 |            |
| createdAt   | datetime | 3    | 创建时间     | Unix时间戳 |
| updatedAt   | datetime | 3    | 记录更新时间 | Unix时间戳 |

### 评论表 Comment

| 字段名称  | 数据类型 | 长度 | 字段描述       | 其他说明        |
| --------- | -------- | ---- | -------------- | --------------- |
| id        | int      |      | 评论id         | 主键            |
| content   | varchar  | 255  | 评论内容       |                 |
| userId    | int      |      | 评论者id       | 外键 User 表 id |
| musicId   | int      |      | 被评论的音乐id | 外键 Music表 id |
| createdAt | datetime | 3    | 创建时间       | Unix时间戳      |

### 评论回复表 Reply

| 字段名称  | 数据类型 | 长度 | 字段描述       | 其他说明          |
| --------- | -------- | ---- | -------------- | ----------------- |
| id        | int      |      | 评论回复id     | 主键              |
| content   | varchar  | 255  | 回复内容       |                   |
| commentId | int      |      | 回复的评论id   | 外键 Comment表 id |
| userId    | int      |      | 评论者id       | 外键 User 表 id   |
| musicId   | int      |      | 被评论的音乐id | 外键 Music表 id   |
| createdAt | datetime | 3    | 创建时间       | Unix时间戳        |

### 首页轮播信息表 Banner

| 字段名称    | 数据类型 | 长度 | 字段描述       | 其他说明       |
| ----------- | -------- | ---- | -------------- | -------------- |
| id          | int      |      | 轮播图id       | 主键           |
| title       | varchar  | 255  | 轮播图标题     |                |
| description | varchar  | 255  | 轮播图描述     |                |
| url         | varchar  | 255  | 轮播图片路径   |                |
| status      | int      |      | 轮播图状态     | 0 正常，1 禁用 |
| href        | varchar  | 255  | 轮播图跳转链接 |                |
| createdAt   | datetime | 3    | 创建时间       | Unix时间戳     |
| updatedAt   | datetime | 3    | 记录更新时间   | Unix时间戳     |

### 多对多关系

- [ ] 歌单和用户，一个用户可以有 N 个歌单，一个歌单可以被 N 个用户共享
- [ ] 歌单和音乐，一个歌单可以有N个音乐，一个音乐可以被N个歌单收录
- [X] 音乐和标签，一个音乐可以有 N 个标签，一个标签可以被 N 个音乐同时拥有 ✅ 2023-05-05
- [X] 歌单和标签，一个歌单可以有 N 个标签，一个标签可以被 N 个歌单同时拥有 ✅ 2023-05-05

### 一对多关系

- [ ] 创建用户和歌单，一个用户可以创建 N 个歌单，一个歌单被一个用户创建
- [ ] 用户和评论，一个用户可以发表多条评论，而一条评论只属于一个用户。
- [ ] 分区和标签之间的关系是一对多，一个分区可以有多个标签，但一个标签只能属于一个分区
- [ ] 音乐和评论之间的关系是一对多，一个音乐可以拥有多个评论，而每个评论只属于一个音乐。
- [ ] 用户和评论回复之间的关系是一对多，一个用户可以回复多条评论，而一条评论回复只属于一个用户。
- [ ] 评论和评论回复之间的关系是一对多，一个评论可能有多个评论回复，但一个评论回复只回复一个评论的。

### 关联表

#### 用户和歌单之间的关联表 (User_Playlist)

- user_id: 外键，关联到用户表中的id字段
- playlist_id: 外键，关联到歌单表中的id字段

#### 歌单和音乐之间的关联表 (Playlist_Music)

- playlist_id: 外键，关联到歌单表中的id字段
- music_id: 外键，关联到音乐表中的id字段

#### 音乐和标签之间的关联表 (Musics_Tag)

- music_id: 外键，关联到音乐表中的id字段
- tag_id: 外键，关联到标签表中的id字段

#### 标签和分区之间的关联表 (Tag_Category)

- tag_id: 外键，关联到音乐表中的id字段
- category_id: 外键，关联到分区表中的id字段

#### 用户和评论之间的关联表 (User_Comment)

- user_id: 外键，关联到用户表中的id字段
- comment_id: 外键，关联到评论表中的id字段

#### 音乐和评论之间的关联表 (Music_Comment)

- music_id: 外键，关联到音乐表中的id字段
- comment_id: 外键，关联到评论表中的id字段

#### 用户和评论回复之间的关联表 ( User_Reply )

- user_id: 外键，关联到用户表中的id字段
- reply_id: 外键，关联到评论回复表中的id字段

#### 评论和评论回复之间的关联表 (Comment_Reply)

- comment_id: 外键，关联到评论表中的id字段
- reply_id: 外键，关联到评论回复表中的id字段

## 接口设计

### 项目 TODO

#todo

- [X] 初始环境搭建
- [X] 功能设计
- [X] 数据库设计

### 功能模块 TODO

#todo

- [x] ⏫ 普通用户模块 ✅ 2023-05-10
    - [x] ⏫ 注册 ing ✅ 2023-05-10
    - [x] ⏫ 登录 ✅ 2023-05-10
    - [ ] 修改用户信息
    - [ ] 修改用户密码
    - [ ] 历史听歌记录
- [ ] 管理员模块
    - [ ] 注册 ing
    - [x] 登录 ✅ 2023-05-10
    - [ ] 添加其他管理员/用户
    - [ ] 修改所有用户信息
- [ ] 音乐模块
    - [x] ⏫ 查询音乐 ✅ 2023-05-11
        - [x] 分页查 ✅ 2023-05-11
        - [x] 排序 + 分页查询 ✅ 2023-05-11
    - [x] ⏫ 添加新音乐（管理员）✅ 2023-05-10
        - [x] 上传音乐封面 ✅ 2023-05-10
        - [x] 上传音乐资源文件 ✅ 2023-05-10
    - [x] ⏫ 删除音乐（管理员） ✅ 2023-05-14
    - [x] 更新音乐信息（管理员） ✅ 2023-05-14
- [ ] 歌单模块
    - [ ] 查询歌单信息
    - [ ] 添加新歌单
    - [ ] 删除歌单（普通用户仅修改自己的）
    - [ ] 更新歌单信息（普通用户仅修改自己的）
- [ ] 标签模块
    - [x] 排序 + 分页查 ✅ 2023-05-17
    - [x] 增加标签 ✅ 2023-05-17
    - [x] 更新标签 ✅ 2023-05-17
    - [x] 删除标签 ✅ 2023-05-18
    - [x] 根据标签查询音乐 ✅ 2023-05-18
- [ ] 分区模块
    - [ ] 添加分区（自定义分区封面）
    - [ ] 删除分区
- [ ] 推荐模块
    - [ ] 随机推荐
    - [ ] 前 xxx 推荐

## 配置 pm2

mac下 安装 pm2

```bash
npm install -g pm2
```

项目根目录 生成基础配置文件
```bash
pm2 init simple
```

生产文件 `ecosystem.config.js` 如下：

```js
module.exports = {
  apps : [{
    name   : "app1",
    script : "./app.js"
  }]
}
```

改一下～

```js
module.exports = {
  apps: [
    {
      name: 'app1',
      script: './src/app.ts', // 换成咱项目里的入口文件～
    },
  ],
};
```

启动、停止、重启、重载、删除配置文件中所有项目

```bash
pm2 start ecosystem.config.js
pm2 stop ecosystem.config.js
pm2 restart ecosystem.config.js
pm2 reload ecosystem.config.js
pm2 delete ecosystem.config.js
```

诶，启动提示 `[PM2][ERROR] Error: Interpreter /opt/homebrew/lib/node_modules/pm2/node_modules/.bin/ts-node is NOT AVAILABLE in PATH. (type 'which /opt/homebrew/lib/node_modules/pm2/node_modules/.bin/ts-node' to double check.)`

好吧，没找到 `ts-node` ，配置文件中加上：

```js
module.exports = {
  apps: [
    {
      name: 'app1',
      script: './src/app.ts',
      interpreter: './node_modules/.bin/ts-node', // +
    },
  ],
};
```

再次 `pm2 start ecosystem.config.js` 好啦
之后就可以是  `pm2 start app1` 了，很完美。

`pm2 list` 可以查看所有app

- `pm2 start app1` 启动
- `pm2 log app1` 查看日志
- `pm2 restart app1` 重启
- `pm2 stop app1` 停止
- `pm2 list` 查看

## 配置七牛云上传

官网：[七牛云上传下载操作指南 - 七牛开发者中心](https://developer.qiniu.com/kodo/kb/1336/upload-download-instructions)
- [node+vue实现文件服务端上传直传七牛云服务器 - 简书](https://www.jianshu.com/p/303d2aa017c2)


首先编写一个上传图片的接口，express中需要使用 [Express multer middleware](http://expressjs.com/en/resources/middleware/multer.html) 解析：

```sh
yarn add multer
```

上传文件的接口中加上中间件 `upload.single('file')`
则可以通过请求 body 中的 file 字段取到文件，如图：

œ

### 安装 qiniu

```bash
yarn add qiniuœœ
```

œ