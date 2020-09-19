---
title: project errors
date: 2020-08-10 20:23:09
tags:
---

## eslint

### 1. error  Don't use `{}` as a type. `{}` actually means "any non-nullish value"

  use `Record<string, unknown>` instead of {}

  ```js
  //incorrect
  post: (url: string, body: {}) =>
  axios.post(url, body).then(responseBody)

  //correct
  post: (url: string, body: Record<string, unknown>) =>
  axios.post(url, body).then(responseBody)
  ```

### 2. Do not access Object.prototype method 'hasOwnProperty' from target object

```js
// incorrect
data.errors.hasOwnProperty("id")
// correct
Object.prototype.hasOwnProperty.call(data.errors,"id")
```

## frontend

### Type 'unknown' is not assignable to type 'ReactNode'

```js
// incorrect, need to specify Object.values' type
Object.values(error.data.errors)  
  .flat()
  .map((el, index) => {
    return <Message.Item key={index}>{el}</Message.Item>;
  })

//correct
Object.values<string>(error.data.errors)  
  .flat()
  .map((el, index) => {
    return <Message.Item key={index}>{el}</Message.Item>;
  })
```

## .Net Core

### 1.'“Value”是 命名空间，但此处被当做 类型 来使用

```cs
namespace Application.Value {
    public interface IValueService {
        Task<ActionResult<ICollection<Value>>> GetValues ();
    }
}
```

Value and Application.Value are duplicate, change namespace to `namespace Application.ValueService`.
