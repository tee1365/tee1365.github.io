---
title: Creating New Blog 2
date: 2020-08-10 23:53:09
tags:
---

### 1. Create new project via create-react-app

```text
npx create-react-app my-app --template typescript
```

### 2. eslint config

eslint, airbnb rules, typescrpt support and prettier support.

I installed eslint@6.6.0 instead of eslint@latest since there is some compatible issues.

```json
{
  "env": {
    "browser": true,
    "es6": true
  },
  "extends": [
    "airbnb",
    "airbnb/hooks",
    "plugin:react/recommended",
    "plugin:import/typescript",
    "plugin:@typescript-eslint/recommended",
    "prettier",
    "prettier/react",
    "prettier/@typescript-eslint"
  ],
  "globals": {
    "Atomics": "readonly",
    "SharedArrayBuffer": "readonly"
  },
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": 2018,
    "sourceType": "module",
    "project": "./tsconfig.json"
  },
  "plugins": ["react", "@typescript-eslint", "prettier"],
  "rules": {
    "prettier/prettier": 2,
    "react/jsx-filename-extension": [1, { "extensions": [".jsx", ".tsx"] }],
    "react-hooks/exhaustive-deps": "warn",
    "@typescript-eslint/indent": "off",
    "quotes": [2, "double"],
    "react/jsx-props-no-spreading": "off",
    "react/prop-types": "off",
    "no-param-reassign": 0,
    "react/display-name": "off",
    "global-require": 0,
    "import/extensions": [
      "error",
      "ignorePackages",
      {
        "js": "never",
        "jsx": "never",
        "ts": "never",
        "tsx": "never"
      }
    ]
  }
}
```

### 3. React Router

add router in index with history, add route in app.

```ts
// api/createBrowserHistory
import { createBrowserHistory } from "history";
export default createBrowserHistory();

// index
<Router history={history}>
  <App />
</Router>

// app
<>
  <Route path="/" component={Homepage} exact />
  <Route path="/value" component={Value} />
  <Route path="/test" component={Test} />
</>
```

### 4. Redux

use provider to wrap router in index.

```ts
// index
<Provider store={store}>
  <PersistGate loading={null} persistor={persistor}>
    <Router history={history}>
      <App />
      <ToastContainer position="bottom-right" />
    </Router>
  </PersistGate>
</Provider>

// rootstore
import { configureStore, combineReducers, Action } from "@reduxjs/toolkit";
import { ThunkAction } from "redux-thunk";
import {
  persistStore,
  persistReducer,
  FLUSH,
  REHYDRATE,
  PAUSE,
  PERSIST,
  PURGE,
  REGISTER,
} from "redux-persist";
import AsyncStorage from "@react-native-community/async-storage";
import counterSlice from "./slices/counterSlice";
import userSlice from "./slices/userSlice";

const persistConfig = {
  key: "root",
  storage: AsyncStorage,
  whitelist: ["user"],
};
const { reducer: counter } = counterSlice;
const { reducer: user } = userSlice;
const rootReducer = combineReducers({ counter, user });
const persistedReducer = persistReducer(persistConfig, rootReducer);
export const store = configureStore({
  reducer: persistedReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
      },
    }),
});
const persistor = persistStore(store);

export type RootState = ReturnType<typeof rootReducer>;
export type AppThunk = ThunkAction<void, RootState, null, Action<string>>;
export type AppDispatch = typeof store.dispatch;

export default persistor;

// slice
import { createSlice } from "@reduxjs/toolkit";
import type { RootState } from "../rootStore";

interface CounterState {
  value: number;
}

const initialState: CounterState = {
  value: 0,
};

const counterSlice = createSlice({
  name: "counter",
  initialState,
  reducers: {
    increment(state) {
      state.value += 1;
    },
  },
});

export const selectCount = (state: RootState): number => state.counter.value;

export default counterSlice;


```

### 5. axios

Add cors at backend first.

```cs
// ConfigureServices
services.AddCors (opt => {
  opt.AddPolicy ("CorsPolicy", policy => {
    policy.AllowAnyHeader ().AllowAnyMethod ().WithOrigins ("http://localhost:3000");
  });
});

// Configure
app.UseCors ("CorsPolicy");

```

Install axios, react-toastify and create agent.ts. Use interceptors to handle errors.

```ts
// agent.ts
import axios, { AxiosResponse } from "axios";
import { toast } from "react-toastify";
import { IValue } from "../models/valueModel";
import history from "./routerHistory";

axios.defaults.baseURL = "http://localhost:5000/api";

const responseBody = (
  response: AxiosResponse
): ReturnType<typeof response.data> => response.data;

axios.interceptors.response.use(undefined, (error) => {
  if (error.message === "Network Error" && !error.response) {
    toast.error("Connection error!");
  } else {
    const { status, data, config } = error.response;
    if (status === 404) {
      history.push("/notfound");
    }
    if (
      status === 400 &&
      config.method === "get" &&
      Object.prototype.hasOwnProperty.call(data.errors, "id")
    ) {
      history.push("/notfound");
    }
    if (status === 500) {
      toast.error("Server error!");
    }
  }
  throw error.response;
});

const requests = {
  get: (url: string, params?: URLSearchParams) =>
    axios.get(url, { params }).then(responseBody),
  post: <T>(url: string, body: T) => axios.post(url, body).then(responseBody),
  put: <T>(url: string, body: T) => axios.put(url, body).then(responseBody),
  del: (url: string) => axios.delete(url).then(responseBody),
  postForm: (url: string, file: Blob) => {
    const formData = new FormData();
    formData.append("File", file);
    return axios
      .post(url, formData, {
        headers: { "Content-type": "multipart/form-data" },
      })
      .then(responseBody);
  },
};

const Value = {
  getValues: (): Promise<IValue[]> => requests.get("/value"),
  getValue: (id: number): Promise<IValue> => requests.get(`/value/${id}`),
};

export default Value;
```

### 6. Semantic UI

npm install semantic-ui-react semantic-ui-css

```ts
// index
import 'semantic-ui-css/semantic.min.css'
```
