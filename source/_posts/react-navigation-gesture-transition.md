---
title: react navigation 手势操作 + 自定义转场动画
date: 2019-12-11 19:06:05
categories:
  - 开发杂记
---

##### 最终达到的一个效果

snack 地址: [https://snack.expo.io/@midoushitongtong/react-navigation-gesture-transition](https://snack.expo.io/@midoushitongtong/react-navigation-gesture-transition)

可以通过 [expo](https://expo.io/)，在你的设备上直接运行它。

![](/images/post/4.gif)

<!--more-->

##### 实现步骤

###### 1. 配置转场动画的过渡时间

通过配置 `transitionSpec` 属性，来配置每个页面转场动画的过渡时间，此属性可以针对`特定的页面`进行单独的配置。

```javascript
{
  transitionSpec: {
    // 进入页面的配置
    open: {
      animation: 'timing',
      config: {
        duration: 450,
        easing: Easing.bezier(0.35, 0.39, 0, 1)
      }
    },
    // 关闭页面的配置
    close: {
      animation: 'timing',
      config: {
        duration: 450,
        easing: Easing.bezier(0.35, 0.39, 0, 1)
      }
    }
  }
}
```

###### 2. 配置转场动画的动画效果

通过配置 `transform` 属性，来配置每个页面转场动画的动画效果，动画效果有很多种，例如【`淡入淡出`、`X/Y轴水平平移`、`Z轴水平旋转`、`放大/缩小`，等】，这里主要以`X轴水平平移`作为参考，其他配置类似，此属性可以针对`特定的页面`进行单独的配置。

```javascript
{
  transform: [
    {
      // next：true 为关闭的页面, false 为打开的页面
      translateX: next
        ? Animated.interpolate(next.progress, {
            inputRange: [0, 1],
            // 关闭的页面从 (0 的位置) 水平平移到 (-屏幕宽度 * 三分之一的位置)
            outputRange: [0, -layouts.screen.width * 0.3],
          })
        : Animated.interpolate(current.progress, {
            inputRange: [0, 1],
            // 打开的页面从 (屏幕宽度位置) 水平平移到 (0的位置)
            outputRange: [layouts.screen.width, 0],
          }),
    },
    // ... 需要配置更多的动画效果，可以根据上面的配置依次往下面添加
  ];
}
```

###### 3. 配置手势操作

在 react navigation 中，配置手势操作非常简单，只需配置两个属性`gestureEnabled`以及`gestureDirection`，前者用于开启或关闭手势操作，后者用于控制手势操作为左右滑动还是上下滑动，此属性可以针对`特定的页面`进行单独的配置。

```javascript
{
  gestureEnabled: true,
  gestureDirection: 'horizontal',
  // gestureDirection: 'vertical'
}
```

###### 4. 整合

将上面的配置整合在一起，也就是上面 gif 中第一个转场动画的配置
看起来像这个样子

```tsx
<Stack.Screen
  name="A"
  component={A}
  options={{
    gestureEnabled: true,
    gestureDirection: 'horizontal',
    transitionSpec: {
      open: {
        animation: 'timing',
        config: {
          duration: 450,
          easing: Easing.bezier(0.35, 0.39, 0, 1),
        },
      },
      close: {
        animation: 'timing',
        config: {
          duration: 450,
          easing: Easing.bezier(0.35, 0.39, 0, 1),
        },
      },
    },
    cardStyleInterpolator: ({ current, next, layouts }) => {
      return {
        cardStyle: {
          transform: [
            {
              translateX: next
                ? Animated.interpolate(next.progress, {
                    inputRange: [0, 1],
                    outputRange: [0, -layouts.screen.width * 0.3],
                  })
                : Animated.interpolate(current.progress, {
                    inputRange: [0, 1],
                    outputRange: [layouts.screen.width, 0],
                  }),
            },
          ],
        },
      };
    },
  }}
/>
```

##### 参考资料

[https://callstack.com/blog/custom-screen-transitions-in-react-navigation](https://callstack.com/blog/custom-screen-transitions-in-react-navigation)

[https://reactnavigation.org](https://reactnavigation.org)
