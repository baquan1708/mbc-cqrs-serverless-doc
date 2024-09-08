---
description: アプリケーションに環境変数を追加して検証する方法を学びます。
---

# 環境変数

MBC CQRS サーバーレス フレームワークには、環境変数のサポートが組み込まれており、次のことが可能になります。

- `.env` を使用して環境変数をロードします
- 環境変数を検証する

## Loading Environment Variables

MBC CQRS serverless フレームワークには、環境変数を `.env*` ファイルから `process.env` にロードするためのサポートが組み込まれています。

```
TODO:
# AWS_PROFILE
AWS_ACCESS_KEY_ID=local
AWS_SECRET_ACCESS_KEY=local
AWS_DEFAULT_REGION=ap-northeast-1
# running environment
# local, dev, stg, prod
NODE_ENV=local
# NODE_ENV=dev
# name of application
# APP_NAME=suisss-recruit
APP_NAME=demo
# APP_NAME=cqrs
# set log levels
LOG_LEVEL=info # debug, info, warn, error, verbose
# disable event route for API GW integration
EVENT_SOURCE_DISABLED=false
# DynamoDB endpoint, useful for local development
DYNAMODB_ENDPOINT=http://localhost:8000
DYNAMODB_REGION=ap-northeast-1
# set the limit size for `attributes` of object in DDB
ATTRIBUTE_LIMIT_SIZE=389120 # bytes, refer to https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ServiceQuotas.html#limits-attributes
# S3 endpoint, useful for local development
S3_ENDPOINT=http://localhost:4566
S3_REGION=ap-northeast-1
# save DDB attributes
S3_BUCKET_NAME=local-bucket
# Step Function endpoint, useful for local development
SFN_ENDPOINT=http://localhost:8083
SFN_REGION=ap-northeast-1
SFN_COMMAND_ARN=arn:aws:states:ap-northeast-1:101010101010:stateMachine:command
# SNS endpoint, useful for local development
SNS_ENDPOINT=http://localhost:4002
SNS_REGION=ap-northeast-1
SNS_TOPIC_ARN=arn:aws:sns:ap-northeast-1:101010101010:MySnsTopic
# Cognito endpoint, useful for local development
COGNITO_URL=http://localhost:9229
COGNITO_USER_POOL_ID=local_2G7noHgW
COGNITO_USER_POLL_CLIENT_ID=dnk8y7ii3wled35p3lw0l2cd7
COGNITO_REGION=ap-northeast-1
# AppSync endpoint, useful for local development
APPSYNC_ENDPOINT=http://localhost:4001/graphql
APPSYNC_API_KEY=da2-fakeApiId123456
# SES email endpoint, useful for local development
SES_ENDPOINT=http://localhost:8005
SES_REGION=ap-northeast-1
SES_FROM_EMAIL=email@example.com

# This was inserted by `prisma init`:
# Environment variables declared in this file are automatically made available to Prisma.
# See the documentation for more detail: https://pris.ly/d/prisma-schema#accessing-environment-variables-from-the-schema

# Prisma supports the native connection string format for PostgreSQL, MySQL, SQLite, SQL Server, MongoDB and CockroachDB.
# See the documentation for all the connection string options: https://pris.ly/d/connection-strings

DATABASE_URL="mysql://root:RootCqrs@localhost:3306/cqrs?schema=public&connection_limit=1"
```

## Validate Environment Variables

必要な環境変数が指定されていない場合、または環境変数が特定の検証ルールを満たしていない場合、アプリケーションの起動中に例外をスローするのが標準的な方法です。 `@mbc-cqrs-serverless/core` パッケージを使用すると、これを簡単に行うことができます。

まずはじめに定義しなければいけないもの

- 検証制約のあるクラス
- EnvironmentVariables 拡張クラス

```ts
// env.validation.ts
import { EnvironmentVariables } from "@mbc-cqrs-severless/core";
import { IsUrl } from "class-validator";

export class EnvValidation extends EnvironmentVariables {
  @IsUrl({
    require_tld: false,
  })
  FRONT_BASE_URL: string;
}
```

これを配置したら、次のように `EnvValidation` クラスを `createHandler` 関数の構成引数として渡します。

```ts
import { createHandler } from "@mbc-cqrs-severless/core";

import { EnvValidation } from "./env.validation";
import { MainModule } from "./main.module";

export const handler = createHandler({
  rootModule: MainModule,
  envCls: EnvValidation,
});
```
