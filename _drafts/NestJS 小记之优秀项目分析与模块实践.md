---
title: NestJS 小记之优秀项目分析与模块实践
link: nest-learn-project-1
catalog: true
lang: cn
date: 2023-09-22 14:40:03
tags:
- nest
- 后端
- node
categories:
- [笔记, 后端]
--- 
## 前言

进入 NestJS 的世界可能会让你感到不知所措，尤其是当你面对众多的模块和概念时。本文旨在解决这个问题，通过深入分析优秀的 NestJS 项目，以及介绍常用的 Nest 内置模块，来帮助你更好地理解和应用这个强大的 Node.js 框架。

我将从 [GitHub 上的 Awesome NestJS 列表](https://github.com/nestjs/awesome-nestjs#open-source)中选取优秀项目进行分析，看它们如何使用 NestJS 的各种功能和模块。 此外，我还将详细介绍一些常用的 Nest 内置模块，如 `@nestjs/core`，并通过代码示例展示它们的应用场景。

- [GitHub - nestjs/awesome-nestjs: A curated list of awesome things related to NestJS 😎](https://github.com/nestjs/awesome-nestjs#open-source)

本文还包括一些重要的后端常用术语解释，如 `DTO（Data Transfer Object）`，以帮助你更全面地理解这个框架。

本文不会直接告诉你如何从零开始搭建一个 NestJS 项目，因为这样的教程和文章网上已经有很多。 相反，本文的主要目的是通过深入分析和解读，让你在实际应用中能更加得心应手。当你遇到某个模块或功能不知如何使用时，这篇文章将作为你的参考和灵感来源。

通过这样的角度和方法，我希望能帮助你不仅仅是“会用”，而是“懂得用”NestJS，从而更加自信和高效地进行后端开发。

让我们一起深入 NestJS 的世界，探索它的无限可能性吧！

## 常用 Nest 内置模块

这是扒拉各种 nest 项目的 package.json 总结得出的模块简介：
### @nestjs/core

- NPM: [@nestjs/core](https://www.npmjs.com/package/@nestjs/core)
- 文档: [NestJS Core Module](https://docs.nestjs.com/core/overview)
- **简介**: 这是 NestJS 框架的核心模块，提供了框架的**基础构建块和核心功能**。
- **使用场景**: 用于构建和初始化 NestJS 应用，几乎所有 NestJS 项目都会用到。
- **代码示例**:
```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```
``
### @nestjs/jwt
- NPM: [@nestjs/jwt](https://www.npmjs.com/package/@nestjs/jwt)
- 文档: [NestJS JWT Module](https://docs.nestjs.com/security/authentication#jwt-module)
- **简介**: 这个模块提供了 JWT（JSON Web Tokens）的支持，用于在 NestJS 应用中实现**身份验证和授权**。
- **使用场景**: 身份验证和授权，通常用于保护路由和资源。
- **代码示例**:
```ts
import { JwtService } from '@nestjs/jwt';

@Injectable()
export class AuthService {
  constructor(private readonly jwtService: JwtService) {}

  async generateToken(user: User) {
    return this.jwtService.sign({ user });
  }
}
```

### @nestjs/config
- NPM: [@nestjs/config](https://www.npmjs.com/package/@nestjs/config)
- 文档: [NestJS Config Module](https://docs.nestjs.com/techniques/configuration)
- **简介**: 这个模块用于**管理 NestJS 应用的配置信息**。它支持**环境变量、类型转换**等。
- **使用场景**: 用于管理应用的配置信息，如数据库连接字符串、API 密钥等。
- **代码示例**:
```ts
import { ConfigService } from '@nestjs/config';

@Injectable()
export class AppService {
  constructor(private configService: ConfigService) {}

  getDatabaseUrl(): string {
    return this.configService.get<string>('DATABASE_URL');
  }
}
```
 
### @nestjs/common
- NPM: [@nestjs/common](https://www.npmjs.com/package/@nestjs/common)
- 文档: [NestJS Common Module](https://docs.nestjs.com/controllers)
- **简介**: 这是 NestJS 的一个通用模块，提供了一系列常用的装饰器、助手函数和其他工具。如 `Injectable`, `Module`, `BadRequestException`, `Body`, `Controller`, `Get`, `Param`, `Post`, `Query` 等 
- **使用场景**: 在 Controller 层、Service 层、Module 中都会用到，用于定义路由、依赖注入等。
- **代码示例**:
```ts
import { Controller, Get } from '@nestjs/common';

@Controller('hello')
export class HelloController {
  @Get()
  sayHello(): string {
    return 'Hello World!';
  }
}
```

### @nestjs/axios
- **NPM**: [@nestjs/axios](https://www.npmjs.com/package/@nestjs/axios)
- **文档**: [NestJS Axios Module](https://docs.nestjs.com/techniques/http-module)
- **简介**: 这个模块为 NestJS 提供了 Axios HTTP 客户端的封装，使得在 NestJS 应用中进行 HTTP 请求更加方便。 如 `HttpService`, `HttpModule`
- **使用场景**: 在 Service 层进行 HTTP 请求，如调用第三方 API。
- **代码示例**:
```ts
import { HttpService } from '@nestjs/axios';

@Injectable()
export class ApiService {
  constructor(private httpService: HttpService) {}

  async fetchData(url: string) {
    const response = await this.httpService.get(url).toPromise();
    return response.data;
  }
}
```

实际项目一般会自行重写http Service 请求添加统一的日志等，如下: 
```ts
import { HttpService } from '@nestjs/axios';
import { catchError, Observable, tap } from 'rxjs';
import { AxiosError, AxiosRequestConfig, AxiosResponse } from 'axios';
import { BadRequestException, Injectable, Logger } from '@nestjs/common';

@Injectable()
export class HttpClientService {
  constructor(private httpService: HttpService) {}

  private logger: Logger = new Logger(HttpClientService.name);
  
  /**
   * 重写 http Service GET 请求, 打印 Request 和 Response
   * @param url
   * @param config
   */
  get<T = any>(url: string, config?: AxiosRequestConfig): Observable<AxiosResponse<T, any>> {
    // 打印发送请求的信息
    this.logger.log(`GET ${url}`);
    return this.httpService.get<T>(url, config).pipe(
      // 在不改变 Observable 流的情况下打印收到的响应
      tap((response) => this.logger.log(`Response ${url} ${JSON.stringify(response.data)}`)),
      // 捕获错误，并打印错误信息
      catchError((error: AxiosError) => {
        const errorData = JSON.stringify(error.response?.data);
        this.logger.error(`GET Error ${url} ${errorData}`);
        throw new BadRequestException([errorData]);
      }),
    );
  }
}
```

### @nestjs/bull
- **NPM**: [@nestjs/bull](https://www.npmjs.com/package/@nestjs/bull)
- **文档**: [NestJS Bull Module](https://docs.nestjs.com/techniques/queues)
- **简介**: 这个模块提供了 Bull 队列库的封装，用于在 NestJS 应用中处理**后台作业和消息队列**。
- **使用场景**: 后台任务处理，如发送邮件、数据处理等。
- **代码示例**:
```ts
import { Processor, Process } from '@nestjs/bull';
import { Job } from 'bull';

@Processor('audio')
export class AudioProcessor {
  @Process('transcode')
  async transcode(job: Job<number>) {
    // Your logic here
  }
}
```

### @nestjs/cache-manager
- **NPM**: [@nestjs/cache-manager](https://www.npmjs.com/package/@nestjs/cache-manager)
- **文档**: [NestJS Caching](https://docs.nestjs.com/techniques/caching)
- **简介**: 这个模块提供了缓存管理功能，支持多种缓存存储方式，如内存、Redis等
- **使用场景**: 数据缓存，如 **API 响应**、**数据库查询结果**、**会话缓存**等。
- **代码示例**: 在这个示例中，我们使用了 `CacheModule` 来注册缓存，并使用 `CacheInterceptor` 拦截器来自动处理缓存逻辑。这样，当多次访问 `findAll` 方法时，结果会被缓存，从而提高响应速度。

```ts
import { CacheModule, CacheInterceptor, Controller, UseInterceptors } from '@nestjs/common';
import { CachingConfigService } from './caching-config.service';

@Module({
  imports: [
    CacheModule.registerAsync({
      useClass: CachingConfigService,
    }),
  ],
})
export class AppModule {}

@Controller('posts')
export class PostsController {
  @UseInterceptors(CacheInterceptor)
  @Get()
  findAll() {
    // Your logic here
  }
}
```

### @nestjs/mongoose
- **NPM**: [@nestjs/mongoose](https://www.npmjs.com/package/@nestjs/mongoose)
- **文档**: [NestJS Mongoose Module](https://docs.nestjs.com/techniques/mongodb)
- **简介**: 这个模块提供了 Mongoose ODM（对象文档映射）的封装，用于在 NestJS 应用中与 MongoDB 数据库进行交互。
- **使用场景**: 数据库操作，特别是与 MongoDB 数据库的交互。
- **代码示例**:

```ts
import { Schema, Prop, SchemaFactory } from '@nestjs/mongoose';
import { Document } from 'mongoose';

@Schema()
export class Cat extends Document {
  @Prop()
  name: string;
}

export const CatSchema = SchemaFactory.createForClass(Cat);
```

### @nestjs/platform-express
- **NPM**: [@nestjs/platform-express](https://www.npmjs.com/package/@nestjs/platform-express)
- **文档**: [NestJS Overview](https://docs.nestjs.com/)
- **简介**: 这个模块是 NestJS 框架的 Express 适配器，用于在 NestJS 应用中使用 Express.js。
- **使用场景**: 用到其中的 `FileInterceptor` 处理文件上传
- **代码示例**: 在这个示例中，我们使用了 `@nestjs/platform-express` 提供的 `FileInterceptor` 来处理文件上传。这实际上是对 Express 的 `multer` 中间件的封装。

```ts
import { Controller, Post, UploadedFile, UseInterceptors } from '@nestjs/common';
import { FileInterceptor } from '@nestjs/platform-express';
import { diskStorage } from 'multer';

@Controller('upload')
export class UploadController {
  @Post()
  @UseInterceptors(FileInterceptor('file', {
    storage: diskStorage({
      destination: './uploads',
      filename: (req, file, cb) => {
        cb(null, `${Date.now()}-${file.originalname}`);
      },
    }),
  }))
  uploadFile(@UploadedFile() file) {
    return { url: `./uploads/${file.filename}` };
  }
}
```

### @nestjs/schedule
- **NPM**: [@nestjs/schedule](https://www.npmjs.com/package/@nestjs/schedule)
- **文档**: [NestJS Schedule Module](https://docs.nestjs.com/techniques/task-scheduling)
- **简介**: 这个模块提供了任务调度功能，用于在 NestJS 应用中执行定时任务。
- **使用场景**: 定时任务，如每日数据备份、定时推送等。
- **代码示例**:

```ts
import { Cron, CronExpression } from '@nestjs/schedule';

@Injectable()
export class TasksService {
  @Cron(CronExpression.EVERY_5_SECONDS)
  handleCron() {
    // Your logic here
  }
}
```

### @nestjs/swagger
- **NPM**: [@nestjs/swagger](https://www.npmjs.com/package/@nestjs/swagger)
- **文档**: [NestJS Swagger Module](https://docs.nestjs.com/recipes/swagger)
- **简介**: 这个模块用于生成和维护 API 文档，基于 Swagger。
- **使用场景**: 自动生成 API 文档。
- **代码示例**:

```ts
import { ApiProperty } from '@nestjs/swagger';

export class CreateUserDto {
  @ApiProperty()
  username: string;

  @ApiProperty()
  password: string;
}
```


### @nestjs/throttler
- **NPM**: [@nestjs/throttler](https://www.npmjs.com/package/@nestjs/throttler)
- **文档**: [NestJS Throttler](https://docs.nestjs.com/security/rate-limiting)
- **简介**: 这个模块提供了请求限制（Rate Limiting）功能，用于防止 API 被滥用。 有 `ThrottlerGuard`, `SkipThrottle` 等
- **使用场景**: 防止 API 滥用，如限制每分钟请求次数。
- **代码示例**:

```ts
import { ThrottlerGuard } from '@nestjs/throttler';

@Injectable()
export class AppGuard extends ThrottlerGuard {
  // Your logic here
}
```

## 其他包


### class-validator
- **NPM**: [class-validator](https://www.npmjs.com/package/class-validator)
- **文档**: [class-validator: Decorator-based property validation for classes.](https://github.com/typestack/class-validator#installation)
- **简介**: 基于装饰器的属性验证库，用于在 TypeScript 和 JavaScript 类中进行数据验证。内部使用 validator.js 进行验证。
- **使用场景**: 数据验证，如用户输入、请求体等。
- **代码示例**:
```ts
import { IsEmail, IsNotEmpty } from 'class-validator';

export class CreateUserDto {
  @IsNotEmpty()
  name: string;

  @IsEmail()
  email: string;
}
```

### class-validator
- **NPM**: [class-validator](https://www.npmjs.com/package/class-validator)
- **文档**: [class-validator: Decorator-based property validation for classes.](https://github.com/typestack/class-validator#installation)
- **简介**: 基于装饰器的属性验证库，用于在 TypeScript 和 JavaScript 类中进行数据验证。内部使用 validator.js 进行验证。
- **使用场景**: 数据验证，如用户输入、请求体等。
- **代码示例**:

```ts
import { IsEmail, IsNotEmpty } from 'class-validator';

export class CreateUserDto {
  @IsNotEmpty()
  name: string;

  @IsEmail()
  email: string;
}
```

### class-transformer
- **NPM**: [class-transformer](https://www.npmjs.com/package/class-transformer)
- **文档**: [class-transformer GitHub](https://github.com/typestack/class-transformer)
- **简介**: 用于对象与类实例之间的转换，通常与 `class-validator` 配合使用。
- **使用场景**: 对象转换和序列化。
- **代码示例**:

```ts
import { plainToClass } from 'class-transformer';

const user = plainToClass(User, {
  name: 'John Doe',
  email: 'john.doe@example.com'
});
```

### cache-manager
- **NPM**: [cache-manager](https://www.npmjs.com/package/cache-manager)
- **文档**: [cache-manager GitHub](https://github.com/BryanDonovan/node-cache-manager)
- **简介**: 一个灵活、可扩展的缓存模块，支持多种存储方式。
- **使用场景**: 数据缓存，如 API 响应、数据库查询结果等。
- **代码示例**:

```ts
import * as cacheManager from 'cache-manager';

const memoryCache = cacheManager.caching({ store: 'memory', max: 100, ttl: 10 });

async function getUser(id: string) {
  const cachedUser = await memoryCache.get(id);
  if (cachedUser) {
    return cachedUser;
  }

  const user = await fetchUserFromDb(id);
  await memoryCache.set(id, user);
  return user;
}
```
### hashids
- **NPM**: [hashids](https://www.npmjs.com/package/hashids)
- **文档**: [Sqids JavaScript (formerly Hashids)](https://sqids.org/javascript?hashids)
- **简介**: 用于生成短的、唯一的非整数 ID 的库。
- **使用场景**: 生成短链接、唯一标识符等。
- **代码示例**:

```ts
import Hashids from 'hashids';

const hashids = new Hashids();
const id = hashids.encode(12345); // 输出一个短的唯一字符串
```

### ioredis
- **NPM**: [ioredis](https://www.npmjs.com/package/ioredis)
- **文档**: [ioredis GitHub](https://github.com/luin/ioredis)
- **简介**: 一个健全、高效的 Redis 客户端。
- **使用场景**: Redis 数据库操作、缓存管理等。
- **代码示例**:

```ts
import Redis from 'ioredis';

const redis = new Redis();
redis.set('key', 'value');
```

### mongoose
- **NPM**: [mongoose](https://www.npmjs.com/package/mongoose)
- **文档**: [Mongoose Documentation](https://mongoosejs.com/)
- **简介**: 用于 MongoDB 和 Node.js 的对象数据模型（ODM）库。
- **使用场景**: MongoDB 数据库操作、数据模型定义等。
- **代码示例**:

```ts
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema({
  username: String,
  password: String,
});

const User = mongoose.model('User', userSchema);
```

### nestgram
- **NPM**: [nestgram](https://www.npmjs.com/package/nestgram)
- **文档**: [About Nestgram - Nestgram](https://degreetpro.gitbook.io/nestgram/)
- **简介**: 一个用于 NestJS 的 Telegram 机器人库。
- **使用场景**: 在 NestJS 应用中集成 Telegram 机器人。
- **代码示例**:

```ts
import { Nestgram } from 'nestgram';

const nestgram = new Nestgram({ token: 'YOUR_BOT_TOKEN' });
nestgram.on('message', (msg) => {
  // 处理消息
});
```

### nestjs-throttler-storage-redis
- **NPM**: [nestjs-throttler-storage-redis](https://www.npmjs.com/package/nestjs-throttler-storage-redis)
- **文档**: [GitHub - nestjs-throttler-storage-redis](https://github.com/kkoomen/nestjs-throttler-storage-redis#readme)
- **简介**: 用于将 NestJS 的 Throttler 模块与 Redis 集成。
- **使用场景**: 在 NestJS 应用中实现基于 Redis 的请求限制。
- **代码示例**:

```ts
import { ThrottlerModule } from '@nestjs/throttler';
import { RedisThrottlerStorage } from 'nestjs-throttler-storage-redis';

@Module({
  imports: [
    ThrottlerModule.forRoot({
      storage: new RedisThrottlerStorage(),
    }),
  ],
})
export class AppModule {}
```

### ramda
- **NPM**: [ramda](https://www.npmjs.com/package/ramda)
- **文档**: [Ramda Documentation](https://ramdajs.com/)
- **简介**: 一个实用的函数式编程库。
- **使用场景**: 数据转换、函数组合等。
- **代码示例**:

```ts
import * as R from 'ramda';

const addOne = R.add(1);
const result = addOne(2); // 输出 3
```

### redis
- **NPM**: [redis](https://www.npmjs.com/package/redis)
- **文档**: [redis GitHub](https://github.com/NodeRedis/node-redis)
- **简介**: Node.js 的 Redis 客户端。
- **使用场景**: 在 Node.js 应用中与 Redis 数据库进行交互。
- **代码示例**:

```ts
import * as redis from 'redis';
const client = redis.createClient();

client.set('key', 'value');
client.get('key', (err, reply) => {
  console.log(reply); // 输出 'value'
});
```

### reflect-metadata
- **NPM**: [reflect-metadata](https://www.npmjs.com/package/reflect-metadata)
- **文档**: [Metadata Proposal - ECMAScript](https://rbuckton.github.io/reflect-metadata/)
- **简介**: 用于元数据反射 API 的库。
- **使用场景**: 在 TypeScript 中使用装饰器和反射。
- **代码示例**:

```ts
import 'reflect-metadata';

@Reflect.metadata('role', 'admin')
class User {
  constructor(public name: string) {}
}

const metadata = Reflect.getMetadata('role', User);
console.log(metadata); // 输出 'admin'
```

### rxjs
- **NPM**: [rxjs](https://www.npmjs.com/package/rxjs)
- **文档**: [RxJS Documentation](https://rxjs.dev/)
- **简介**: 用于使用可观察对象进行响应式编程的库。
- **使用场景**: 异步编程、事件处理等。
- **代码示例**:

```ts
import { of } from 'rxjs';
import { map } from 'rxjs/operators';

const source$ = of(1, 2, 3);
const result$ = source$.pipe(map(x => x * 2));

result$.subscribe(x => console.log(x)); // 输出 2, 4, 6
```

## 名词解释

### DTO（Data Transfer Object）
`DTO` 是 `Data Transfer Object`（数据传输对象）的缩写，用于在客户端和服务器之间传输数据。在 Nest.js 中，`DTO` 通常用于定义接口的数据结构，以便进行数据验证和转换。

#### 例子

可以看到在有些项目中有 dto 目录，干什么用的呢

```ts
// module/dto/LoginUserDto.ts 
import { IsOptional, IsString } from 'class-validator';

export class LoginUserDto {
  /**
   * 用户名
   */
  @IsString()
  userName: string;
  /**
   * 密码
   */
  @IsString()
  password: string; 
}
```

以上代码定义了用户注册时需要提交的数据格式。使用 [class-validator](https://www.npmjs.com/package/class-validator) 库来进行数据验证。

- **属性解释**
  - `userName`: 用户名，必须是字符串类型。
  - `password`: 密码，必须是字符串类型。

- **装饰器解释**
  - `@IsString()`: 确保字段是字符串类型。
  - `@IsOptional()`: 表示字段是可选的。

更多解释使用详见 https://github.com/typestack/class-validator#usage

#### 用途

1. **数据验证**: 当用户尝试注册时，`LoginUserDto` 用于验证提交的数据是否符合预期的格式。例如，它会检查 `userName` 和 `password` 是否为字符串。

2. **类型安全**: 使用 TypeScript 和 DTO，您可以确保数据结构的类型安全，这有助于减少运行时错误。

3. **文档**: 通过 DTO，其他开发人员可以更容易地了解 API 接口期望的数据格式。

4. **代码重用**: DTO 可以在多个地方重用，例如在不同的服务或控制器中。

总体而言，`LoginUserDto` 类提供了一种结构化和类型安全的方式来处理用户注册的数据。





