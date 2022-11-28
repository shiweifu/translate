本文代码来自：[Drawer navigation | React Navigation](https://reactnavigation.org/docs/drawer-based-navigation/)

侧边栏在 App 开发中，是十分常见的需求。



![image-20221031001622604](C:\Users\shiweifu\AppData\Roaming\Typora\typora-user-images\image-20221031001622604.png)



在 React Native 开发中，侧边栏也是通过 React Navigation 来实现的。它被封装到 Drawer 组件中，引入后即可使用。



```
import * as React from 'react';
import { Button, View } from 'react-native';
import { createDrawerNavigator } from '@react-navigation/drawer';
import { NavigationContainer } from '@react-navigation/native';

function HomeScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Button
        onPress={() => navigation.navigate('Notifications')}
        title="Go to notifications"
      />
    </View>
  );
}

function NotificationsScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Button onPress={() => navigation.goBack()} title="Go back home" />
    </View>
  );
}

const Drawer = createDrawerNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Drawer.Navigator initialRouteName="Home">
        <Drawer.Screen name="Home" component={HomeScreen} />
        <Drawer.Screen name="Notifications" component={NotificationsScreen} />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}
```



执行代码，会看到：



![image-20221031001948688](C:\Users\shiweifu\AppData\Roaming\Typora\typora-user-images\image-20221031001948688.png)

代码首先定义了两个 `Drawer.Screen`，两个 `Screen` 对象被包在了 `Drawer.Navigator` 对象中，直接执行代码，会发现，侧边栏已经根据刚刚的定义，被绘制出来，点击也可以跳转到对应的页面。基础的使用已经实现。