---
title: frontend-setup
date: 2022-01-06 17:33:14
tags:
---

## eslint

[eslint](https://andrebnassis.medium.com/setting-eslint-on-a-react-typescript-project-2021-1190a43ffba)
npm install eslint --save-dev
npx eslint --init
npm install eslint-config-airbnb-typescript --save-dev
npx install-peerdeps --dev eslint-config-airbnb-typescript
npm install --save-dev eslint-config-prettier

## react router

``` js
  let history = useHistory();

  function handleClick() {
    history.push("/home");
  }
```

## problem

1. We don't need to import react to use jsx in react 17, although eslint give errors with import

    ```js
      "react/jsx-uses-react": "off",
      "react/react-in-jsx-scope": "off"
    ```

2. Parsing error “parserOptions.project” has been set for @typescript-eslint/parser

    Add it to the includes in tsconfig.json

    ```js
    "include": [
      ".eslintrc.js",
    ]
    ```
