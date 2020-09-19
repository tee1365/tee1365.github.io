---
title: ArrowCityMobile-Note
date: 2020-09-07 18:15:51
tags:
---

## expo

1. 键盘弹出时影响布局 底部的button被顶到上面
在app.json中设置softwareKeyboardLayoutMode

    ```json
    "android": {
      "softwareKeyboardLayoutMode": "pan"
    }
    ```

## typecript

1. use typeguard helper to solve partial

    ```ts
    const typeGuard = <T, K extends keyof T>(
      obj: T,
      ...props: K[]
    ): obj is T & Required<Pick<T, K>> => {
      return props.every((k) => typeof obj[k] !== "undefined");
    };

    // its hard to use value inside newAccessToken since all property are string|undefined
    const newAccessToken:Partial<IToken>

    // use typeguard
    if(typeGuard(newAccessToken, "accessToken", "accessTokenExpiryDate"){
      const newToken: ILoginToken = {
        refreshToken,
        refreshTokenExpiryDate,
        ...newAccessToken,
      };
      // ...
    }
    ```

2. usage of generic functional components in tsx files

    ```tsx
    // use <T,> to tell compiler this is a generic
    // use <T extends unknown> instend of <T,> works as well
    const A = <T,>(myObject: T) : React.ReactElement => <h1>{myObject}</h1>;

    // usage
    <A<string>/>
    ```

## components

1. component for fullscreen overlay

    ```tsx
    interface OverlayImageStyle extends ViewStyle {
      overlayColor?: string;
    }

    export interface ImageOverlayProps extends ImageBackgroundProps {
      style?: StyleProp<OverlayImageStyle>;
      children: React.ReactNode;
    }

    const DEFAULT_OVERLAY_COLOR = "rgba(0, 0, 0, 0.45)";

    export const ImageOverlay = (
      props: ImageOverlayProps
    ): React.ReactElement<ImageBackgroundProps> => {
      const { style, children, ...imageBackgroundProps } = props;
      const { overlayColor, ...imageBackgroundStyle } = StyleSheet.flatten(style);

      return (
        <ImageBackground {...imageBackgroundProps} style={imageBackgroundStyle}>
          <View
            style={[
              StyleSheet.absoluteFill,
              { backgroundColor: overlayColor || DEFAULT_OVERLAY_COLOR },
            ]}
          />
          {children}
        </ImageBackground>
      );
    };
    ```

## styled-components

1. have to add units
2. use `flex:1 0` instead of `flex:1`

    ```ts
    export const AuthInnerContainer = styled.View<ViewProps>`
      flex: 1 0
      width: ${LayoutConst.width * 0.8}px
      height: ${LayoutConst.height}px
    `;
    ```
