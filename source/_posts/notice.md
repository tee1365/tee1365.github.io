---
title: flicket project notice
date: 2022-06-26 22:59:15
tags:
---

## 1. how to type onChange for Select

  gatewayOptions is the value of option for this component.

   **admin(multi)**:  

   ```ts
   onChange={(val: typeof gatewayOptions[number]["value"][]) => setGateway(val)}
   ```

   **admin(not multi)**:  

   ```ts
   onChange={(val: typeof gatewayOptions[number]["value"]) => setGateway(val)}
   ```

   **customer**:

   ```ts
   onChange={(val: typeof gatewayOptions[number]) => setGateway(val)}
   ```

## 2. add console.error as the second parameter of handleSubmit

## 3. code for repeatable queue

1. get all repeatable jobs

    ```ts
    void this.automationQueue
      .getRepeatableJobs()
      .then((res) => console.log(res));
    ```

2. remove all repeatable jobs

    ```ts
        void this.automationQueue.getRepeatableJobs().then((res) => {
        for (const r of res) {
          void this.automationQueue.removeRepeatableByKey(r.key);
        }
      });
    ```

## 4. testing

1. test webhook:  

    run `ngrok http 3001`  
    run backend  
    `https://1197-101-98-116-49.ngrok.io/graphql`
  
2. to test facebook login:

    local-ssl-proxy --source 3002 --target 3000  
    `https://chiefs.flicket.localhost:3002/`

## 5. future social module

1. Screen recording

    create screen recordings, check Best Practices part when recording
    1. login flow (admin login)
    2. permission granting (admin login)
    3. Data Usage (mention overview and user social tab)

2. no action after finding a user
3. every 50 days -> change refresh token automatically
4. currently igusername can be duplicate in the database
