---
description: インストール
---

# インストール

システム要件:

- [Node.js](https://nodejs.org/en/download/package-manager)
- [JQ cli](https://jqlang.github.io/jq/download/)
- [AWS cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [Docker](https://docs.docker.com/engine/install/)
- macOSとLinuxをサポートしています。

## 自動インストール

To get started, you can scaffold the project with the [mbc-cqrs-serverless CLI](./cli.md). To scaffold the project with the mbc-cqrs-severless CLI, run the following commands. This will create a new project directory, and populate the directory with the initial core mbc-cqrs-severless files and supporting modules, creating a conventional base structure for your project.

```bash
npm i -g @mbc-cqrs-severless/cli
mbc new project-name
```

mbc-cqrs-serverless を使用して新しいプロジェクトを作成した際は、[プロジェクト構造](./project-structure.md) のドキュメントを参照してアプリケーションないで使用出来る全てのファイルとフォルダーの概要を確認して下さい。

## 開発用サーバの実行

1. `cp .env.local .env` コマンドを実行して環境ファイルを作成します。
2. `npm install` コマンドを実行して必要なモジュールをインストールします。
3. `npm run build` コマンドを実行して開発モードでビルドします。
4. 別のターミナルを開いて `npm run offline:docker` コマンドを実行し、開発用サーバをDockerで実行します。
5. 別のターミナルを開いて `npm run migrate` コマンドを実行し開発サーバ内のRDS及びDynamoDBをマイグレーとします。
6. 最後に `npm run offline:sls` コマンドを実行して serverless offline mode を実行します。

サーバの起動が完了したら次のようなメッセージを確認する事が出来ます。

```bash
DEBUG[serverless-offline-sns][adapter]: successfully subscribed queue "http://localhost:9324/101010101010/notification-queue" to topic: "arn:aws:sns:ap-northeast-1:101010101010:MySnsTopic"
Offline Lambda Server listening on http://localhost:4000
serverless-offline-aws-eventbridge :: Plugin ready
serverless-offline-aws-eventbridge :: Mock server running at port: 4010
Starting Offline SQS at stage dev (ap-northeast-1)
Starting Offline Dynamodb Streams at stage dev (ap-northeast-1)

Starting Offline at stage dev (ap-northeast-1)

Offline [http for lambda] listening on http://localhost:3002
Function names exposed for local invocation by aws-sdk:
           * main: serverless-example-dev-main
Configuring JWT Authorization: ANY /{proxy+}

   ┌────────────────────────────────────────────────────────────────────────┐
   │                                                                        │
   │   ANY | http://localhost:3000/api/public                               │
   │   POST | http://localhost:3000/2015-03-31/functions/main/invocations   │
   │   ANY | http://localhost:3000/swagger-ui/{proxy*}                      │
   │   POST | http://localhost:3000/2015-03-31/functions/main/invocations   │
   │   ANY | http://localhost:3000/{proxy*}                                 │
   │   POST | http://localhost:3000/2015-03-31/functions/main/invocations   │
   │                                                                        │
   └────────────────────────────────────────────────────────────────────────┘

Server ready: http://localhost:3000 🚀
```

次のサービスのエンドポイントが起動します。:

- API gateway: http://localhost:3000
- Offline Lambda Server: http://localhost:4000
- HTTP for lambda: http://localhost:3002
- Step functions: http://localhost:8083
- DynamoDB: http://localhost:8000
- DynamoDB admin: http://localhost:8001
- SNS: http://localhost:4002
- SQS: http://localhost:9324
- SQS admin: http://localhost:9325
- Localstack: http://localhost:4566
- AppSync: http://localhost:4001
- Cognito: http://localhost:9229
- EventBridge: http://localhost:4010
- Simple Email Service: http://localhost:8005
- `npx prisma studio` を実行して prisma studio を起動します。 エンドポイント: http://localhost:5000

:::note

ローカル開発環境で `npm run migrate` コマンドやローカルの Cognito にログイン出来ない場合は次のコマンドを使用してファイルやフォルダーにアクセス権を設定する必要があります。

```bash
sudo chmod -R 777 ./infra-local/cognito-local
sudo chmod -R 777 ./infra-local/cognito-local/db/clients.json
sudo chmod -R 777 ./infra-local
sudo chmod -R 777 ./infra-local/docker-data/
sudo chmod -R 777 ./infra-local/docker-data/dynamodb-local
```

:::