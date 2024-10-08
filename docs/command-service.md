---
description: { { Learn how to use CommandService. } }
---

# {{CommandService}}

## {{Description}}

{{The methods of this service are primarily used to manipulate the command table.}}

{{In the example for the method below, assume you import the `CommandModule` into your module as follows:}}

```ts
import { CommandModule } from "@mbc-cqrs-severless/core";
import { Module } from "@nestjs/common";

import { CatDataSyncRdsHandler } from "./handler/cat-rds.handler";
import { CatController } from "./cat.controller";
import { CatService } from "./cat.service";

@Module({
  imports: [
    CommandModule.register({
      tableName: "cat",
      dataSyncHandlers: [CatDataSyncRdsHandler],
    }),
  ],
  controllers: [CatController],
  providers: [CatService],
})
export class CatModule {}
```

{{Then, the `CommandService` and `DataService` will be ready for injection into other services for your use.}}

## {{Methods}}

### {{*async* `publish(input: CommandInputModel, opts: ICommandOptions)`}}

{{Utilize this method to publish a full command, as it will insert the data directly into the **command** table.}}

{{For example, you can publish a new cat command as bellow:}}

```ts
import {
  generateId,
  getCommandSource,
  VERSION_FIRST,
} from "@mbc-cqrs-severless/core";

// ...

const catCommand = new CatCommandDto({
  pk: catPk,
  sk: catSk,
  tenantCode,
  id: generateId(catPk, catSk),
  code,
  type: "CAT",
  name: attributes.name,
  version: VERSION_FIRST,
  attributes,
});

const commandSource = getCommandSource(
  basename(__dirname),
  this.constructor.name,
  "createCatCommand"
);

const item = await this.commandService.publish(catCommand, {
  source: commandSource,
  invokeContext,
});
```

{{The method returns the command data.}}

### {{*async* `publishPartialUpdate( input: CommandPartialInputModel, opts?: ICommandOptions)`}}

{{This method allows you to create new command data based on the previous command.}}

{{For example, you want to update cat's name:}}

```ts
import {
  generateId,
  getCommandSource,
  VERSION_FIRST,
} from '@mbc-cqrs-severless/core'

// ...

  const catCommand = new CatCommandDto({
    pk: catPk,
    sk: catSk,
    tenantCode,
    id: generateId(catPk, catSk),
    code,
    type: 'CAT',
    name: attributes.name,
    version: VERSION_FIRST,
    attributes,
  })
  
  const commandSource = getCommandSource(
    basename(__dirname),
    this.constructor.name,
    'createCatCommand',
  )
  
  const item = await this.commandService.publish(catCommand,{
    source: commandSource,
    invokeContext,
  })
```

{{The method returns the command data.}}

### {{async reSyncData()}}

{{If you want to reapply the data sync handler, this method is designed for you to use. You only need to call the function as follows:}}

```ts
await this.commandService.reSyncData();
```
