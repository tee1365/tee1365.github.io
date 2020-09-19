---
title: ArrowCityMobile Setup
date: 2020-09-01 01:31:57
tags:
---


## redux toolkit

1. [modify state using immer](https://immerjs.github.io/immer/docs/return)
2. serializableCheck has conflict with redux persist, avoid conflict by using ignoredActions
  
```ts
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

  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
      },
    }),
```

## react navigation

1. navigate in redux actions  
create a ref and assign to NavigationContainer

```ts
//navigationHelpers.ts
const navigationRef = React.createRef<NavigationContainerRef>();
//Navigation.tsx
<NavigationContainer
 linking={LinkingConfiguration}
 ref={navigationHelpers.navigationRef}
>
 <RootNavigator />
</NavigationContainer>
```

then use ref to create a custom navigate method

```ts
//navigationHelpers.ts
const navigate = <RouteName extends keyof ParamListBase>(
  name: string,
  params?: ParamListBase[RouteName]
): void => {
  navigationRef.current?.navigate(name, params);
};
```

## react native debugger

1. network inspection  
   use `http://10.0.2.2:5000/api/mob` when network inspection disabled
   use `http://localhost:5000/api/mob` when network inspection enabled

## Form using Formik and yup

```ts
<Formik
 initialValues={{ email: "", password: "", isRememberMe: false }}
 onSubmit={(values: ILogin) => {
   dispatch(loginAsync(values));
 }}
 validationSchema={yup.object().shape({
   email: yup.string().email().required(),
   password: yup.string().required(),
 })}
>
 {({
   values,
   handleChange,
   handleSubmit,
   setFieldValue,
   setFieldTouched,
   touched,
   errors,
   isValid,
 }) => (
   <>
     <View style={styles.inputContainer}>
       <TextInput
         style={styles.input}
         mode="outlined"
         label="Email"
         value={values.email}
         onChangeText={handleChange("email")}
         onBlur={() => setFieldTouched("email")}
       />
       <HelperText
         type="error"
         visible={touched.email && !!errors.email}
       >
         {errors.email}
       </HelperText>
       <TextInput
         style={styles.input}
         mode="outlined"
         label="Password"
         value={values.password}
         onChangeText={handleChange("password")}
         onBlur={() => setFieldTouched("password")}
         secureTextEntry
       />
       <HelperText
         type="error"
         visible={!!(touched.password && !!errors.password)}
       >
         {errors.password}
       </HelperText>
       <View style={styles.switchContainer}>
         <Text>Remember Me</Text>
         <Switch
           value={values.isRememberMe}
           onValueChange={(value) =>
             setFieldValue("isRememberMe", value)
           }
         />
       </View>
     </View>
     <Button
       icon="login"
       mode="contained"
       color={colors.accent}
       onPress={handleSubmit}
       disabled={!isValid}
     >
       Submit
     </Button>
   </>
 )}
</Formik>
```

## axios

1. set request interceptor in app.tsx

```ts
//App.tsx
const { token } = store.getState().user;
useEffect(() => {
 if (token.accessToken) {
   setClientToken(token.accessToken);
 }
}, [token.accessToken]);
//agent.ts
export const setClientToken = (token: string): void => {
  axios.interceptors.request.use((config: AxiosRequestConfig) => {
    config.headers.Authorization = `Bearer ${token}`;
    return config;
  });
};
```

setting request interceptor in app.tsx can prevent dependency cycle.

```ts
//without setting request interceptor in app.tsx

//agent
import store from rootstore

//rootstore
import userReducer from userReducer

//userReducer
import {AuthApi} from agent
```
