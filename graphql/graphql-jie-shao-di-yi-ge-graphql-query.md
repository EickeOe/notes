# graphql介绍&第一个GraphQL Query

## GraphQL是什么？

GraphQL官方解释是一个用于API的查询语言。这样说起来可能不太好理解。

例如一个用于请求用户数据的传统的restFulAPI：

![](../.gitbook/assets/image%20%283%29.png)

这个API始终会返回id,name,avatar三个属性。

如果我们不需要avatar这个属性呢？

我们可以让后端的小哥哥小姐姐帮忙去掉avatar这条属性，或者新写一个接口做这个事情。

那么，可不可以前端去控制需要的字段？

答案是可以的，GraphQL为此而生。下面的代码块是一个graphQL查询语句，当我们只需要account的id和name时这样写查询就可以了，如果需要avatar，我们再加上avatar就可以了。

```graphql
{
  account {
    id
    name
  }
}
```

这样对比restFulAPI而言，graphQL更加灵活，能够准确获取想要的数据。

结论：查询语句放在前端，由前端来控制需要的数据，所以是用于API的查询语言。

## 为什么要使用GraphQL？

* 字段冗余
* 获取多个数据，只用一个请求
* 清晰的描述所有的数据类型，数据结构

## 如何使用GraphQL？

这里我用nestjs作为后端服务器，不熟悉nestjs的小伙伴可以点[这里](https://docs.nestjs.com/)学习一下nestjs。

1. 创建后端服务

```bash
npm i -g @nestjs/cli
nest new graph-ql
cd graph-ql
```

2. 安装graphQL相关依赖

```bash
yarn add @nestjs/graphql apollo-server-express graphql-tools graphql
```

3. 在ApplicationModule中引用GraphQLModule

```typescript
import { Module } from '@nestjs/common';
import { GraphQLModule } from '@nestjs/graphql';

@Module({
  imports: [
    GraphQLModule.forRoot({}),
  ],
})
export class ApplicationModule {}
```

4. 设置Schema的路径，这样，GraphQL就可以检测到我们的graphql文件了

```typescript
GraphQLModule.forRoot({
  typePaths: ['./**/*.graphql'],
}),
```

5. 创建我们的account查询

在src下新建名为account的文件夹，及相关代码文件

```graphql
# account.graphql
type Account {
	id: Int
	name: String
	avatar: String
}
type Query {
	account(id: ID!): Account
}
```

```typescript
// account.schema.ts
export abstract class Account {
    id?: number;
    name?: string;
    avatar?: string;
}
```

```typescript
// account.resolvers.ts
import { ParseIntPipe } from '@nestjs/common';
import { Args, Query, Resolver } from '@nestjs/graphql';
import { Account } from './account.schema';

const account: Account = {
  id: 0,
  name: 'eick',
  avatar: 'https://avatars0.githubusercontent.com/u/22267244'
}

@Resolver('Account')
export class AccountResolvers {
  @Query('account')
  async account(@Args('id', ParseIntPipe) id: number): Promise<Account> {
    return account;
  }
}
```

```typescript
// account.module.ts
import { Module } from '@nestjs/common';
import { AccountResolvers } from './account.resolvers';

@Module({
  imports: [],
  providers: [AccountResolvers],
})
export class AccountModule { }
```

6. 在AppModule中引用AccountModule

```typescript
// app.module.ts
import { Module } from '@nestjs/common';
import { GraphQLModule } from '@nestjs/graphql';
import { AccountModule } from './account/account.module';

@Module({
  imports: [
    AccountModule,
    GraphQLModule.forRoot({
      typePaths: ['./**/*.graphql'],
    }),
  ],
})
export class AppModule { }
```

7. 启动nestjs服务

```bash
yarn run start:dev
```

8. 访问[http://localhost:3000/graphql](http://localhost:3000/graphql)，打开Playground查询界面

![](../.gitbook/assets/image%20%281%29.png)

这样，我们的第一个graphQL就已经写好啦。

我们可以在Playground的左侧面板中输入查询语句：

```graphql
{
  account(id:1){
    name
  }
}
```

点击查询按钮,就会出现我们需要的数据啦：

![](../.gitbook/assets/image%20%282%29.png)

到这里，graphQL的一个简单的query就完成啦，示例中没有关联数据库，可以自行在resolver中查询数据库。

