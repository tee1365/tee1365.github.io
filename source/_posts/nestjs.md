---
title: nestjs
date: 2022-01-06 15:07:28
tags:
---

## dynamic module

Dynamic module allows us to create customizable modules. In the following example, `HttpModule.register({})` create a new httpmodule with no configuration. If we don't do this, we can't set configuration properly(you may get baseURL from other places even if you set a new baseURL here). `TypeOrmModule.forFeature([StoryWebhook])` is a dynamic module as well.

```ts
@Module({
  imports: [TypeOrmModule.forFeature([StoryWebhook]),HttpModule.register({})],
  controllers: [SocialController],
  providers: [SocialService, SocialIgStoryService, SocialHttpService, SocialIgStoryResolver],
  exports: [SocialService]
})
export class SocialModule {}
```

## handle response in http module
