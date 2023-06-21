---
abbrlink: ''
categories:
- - node
date: '2023-06-21T15:26:21.527934+08:00'
tags:
- NestJS
title: NestJS介绍
updated: 2023-6-21T15:29:58.254+8:0
---
### **简介**

Nest 是一个用于构建高效，可扩展的 [Node.js](http://nodejs.cn/) 服务器端应用程序的框架。它使用渐进式 JavaScript，内置并完全支持 [TypeScript](https://www.tslang.cn/)（但仍然允许开发人员使用纯 JavaScript 编写代码）并结合了 OOP（面向对象编程），FP（函数式编程）和 FRP（函数式响应编程）的元素。

### **NestJS优点**

1. 支持 TypeScript，使语法更加规范
2. 兼容 Express 中间件，Express 是最早出现的轻量级的 node server端框架
3. 层层处理，可以约束代码，比如何时使用中间件、使用 Guards守卫 等
4. 依赖注入及模块化的思想，提供了完整的 MVC 链路，使代码结构清晰，便于维护

### **核心概念**

#### 1、**控制器Controller**

客户端的请求，最终交给 哪个函数 / 模块 ，都需要通过预先处理，直接处理客户端请求（路由、方法等）的模块，称之为 控制器

  控制器原理：

  控制器：接收应用的特定请求

  路由机制：控制哪个控制器接收哪些请求

  控制器及路由的关系：一个控制器有多个路由，不同路由 执行 不同操作

![](file:///C:\Users\11296\AppData\Local\Temp\ksohtml31120\wps1.jpg)

#### **2、****提供者** **Provider**

Providers 是 Nest 的一个基本概念。几乎所有的东西都可以被认为是提供者（例如：service, repository, factory, helper...）

提供者的本质：用 @Injectable() 装饰器 注解的简单类

提供者 Provider 可以通过 constructor 注入依赖关系（即创建各种关系）

```javascript
import { Injectable } from '@nestjs/common';

@Injectable()
export class UserService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }
  findAll(): Cat[] {
    return this.cats;
  }
}
```

#### **3、模块Module**

模块是用 @Module() 装饰器 注解的简单类

@module() 装饰器接受一个 描述模块属性的对象

l providers：服务提供者列表，本模块可用，会自动注入

l controllers：控制器列表，本模块可用，会绑定路由访问

l imports：本模块导入的模块，如需要使用其他模块的 provider，则必须导入其他模块

l exports：本模块导出的服务提供者，在此处定义的 provider 才能被其他模块使用

```javascript
@Module({
  imports: [
    TypeOrmModule.forFeature([UserEntity]),
  ],
  controllers: [UserController],
  providers: [UserService],
  exports: [UserService]
})
export class UserModule { }
```

#### **4、****中间件 Middleware**

客户端和路由处理 的中间，在路由处理程序之前，调用的 函数

中间件函数可以访问：请求对象、响应对象、请求响应周期中的 next() 中间件函数

![](file:///C:\Users\11296\AppData\Local\Temp\ksohtml31120\wps2.jpg)中间件函数的任务

1. 执行任何代码
2. 对请求和响应对象进行更改
3. 结束请求-响应周期
4. 调用堆栈中的下一个中间件函数
5. 如果当前中间件函数，没有结束 请求-响应周期，它必须调用 next() 将控制传递给下一个中间件函数，否则，请求将被挂起

#### **5、异常过滤器 Filter**

内置的异常层负责处理整个应用程序中的所有抛出的异常。当捕获到未处理的异常时，最终用户将收到友好的响应。

当异常无法识别时 (既不是 HttpException 也不是继承 HttpException 的类 ) , 将会收到以下 JSON 响应：

```javascript
{
    "statusCode": 500,
    "message": "Internal server error"
}
```

在异常过滤器中对响应的报错信息进行处理，统一错误信息格式

```javascript
{
      data: {}, 
      msg: message,
      code: '400',
      url: '/api/user',
      success: false,
    }
```

#### **6、管道 Pipe**

管道作用：

1.转换：将输入数据转换为所需的格式输出

2.验证：验证输入的内容是否满足预先定义的规则，当数据不正确时可能会抛出异常

使用第三方插件：class-validator，配合管道使用对参数进行校验

#### 7、**守卫 Guard**

守卫：实现权限验证，比如验证请求是否包含 token 或者 token 是否过期

与 中间件 Middleware 相比，守卫 Guard 能获得更详细的关于请求的执行上下文信息

通常 Guards，位于 Middleware 之后，Pipe 之前（请求正式被处理函数处理之前）

#### 8、**拦截器 Interceptor**

拦截器作用：

1. 在函数执行前/后，绑定额外逻辑
2. 转换从函数返回的结果
3. 转换从函数抛出的异常
4. 重写函数

举个栗子：对所有接口返回的 数据结构 处理（应用拦截器的）

```javascript
{
  data,

  code: '200',

  msg: '请求成功',

  success: true,
}
```
