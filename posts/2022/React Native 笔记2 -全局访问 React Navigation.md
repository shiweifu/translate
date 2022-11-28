React Navigation 是 React Native 页面导航的试试标准。



官方默认的使用方式是通过组件的 `navigation` 属性来进行页面切换。所有在路由中注册过的页面，都会被注入这个属性，在组件中可用访问到：



```
// 官方页面切换示例
import * as React from 'react';
import { Button, View, Text } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

function HomeScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>Home Screen</Text>
      <Button
        title="Go to Details"
        onPress={() => navigation.navigate('Details')}
      />
    </View>
  );
}

```



这种使用方式，是和组件绑定的，组件之间跳转的代码，必须在组件中，此时工作的很好。但有的时候，我们还需要在组件之外访问导航和切换页面，如网络请求中间层、全局状态变化等，React Navigation 也提供了解决方案。



React Navigation 提供了 `createNavigationContainerRef` 方法，来创建对组件的引用，在 App 初始的时候，对该引用进行赋值，然后即可在 App 任意位置进行访问。



还是以官方的代码作为示例，首先创建用于全局访问的文件：



```
// RootNavigation.js

import { createNavigationContainerRef } from '@react-navigation/native';

export const navigationRef = createNavigationContainerRef()

export function navigate(name, params) {
  if (navigationRef.isReady()) {
    navigationRef.navigate(name, params);
  }
}

// add other navigation functions that you need and export them
```



然后，在 App 初始化阶段，对引用进行赋值：



```
// App.js

import { NavigationContainer } from '@react-navigation/native';
import { navigationRef } from './RootNavigation';

export default function App() {
  return (
    <NavigationContainer ref={navigationRef}>{/* ... */}</NavigationContainer>
  );
}
```



赋值工作已经完成，此时可以在需要访问导航组件的地方，引用这个新创建的文件，调用对应方法即可。



```
// any js module
import * as RootNavigation from './path/to/RootNavigation.js';

// ...

RootNavigation.navigate('ChatScreen', { userName: 'Lucy' });
```



本文代码示例来自官方文档：

[Navigating without the navigation prop | React Navigation](https://reactnavigation.org/docs/navigating-without-navigation-prop/)













