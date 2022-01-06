---
title: common
date: 2022-01-06 17:23:52
tags:
---

## 1. type configuration

  Error: Type attribute is set as `"postgresql|sqlite...."`. If we pass "postgresql" in, then it will seen as a string instead of "postgresql|sqlite....".

  Since this configuration is consumed by `MikroORM.init`, we can get the type which should be passed like this. This will solve the error and give us auto code completion.

  ```ts
  export default {
    dbName: 'lireddit',
    type: 'postgresql',
    debug: !__prod__,
    entities: [Post],
    migrations: {
      path: path.join(__dirname, './migrations'),
      pattern: /^[\w-]+\d+\.[tj]s$/,
    },
  } as Parameters<typeof MikroORM.init>[0];
  ```

## 2. add new type to req.session in express

  If we write something like this `req.session.userId = user.id;`, it will give a error, because there is no `userId` in the `req.session`. The type of `req.session` is set by other libraries, so we need declaration merging to add new types into it. We can write declaration merging like the following code in the file where session is used. The type will be merged into the original type automatically.

  ```ts
  // resolver/user.ts
  declare module 'express-session' {
    interface Session {
      userId: number;
    }
  }
  ```
