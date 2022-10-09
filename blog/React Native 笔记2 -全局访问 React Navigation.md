React Navigation 是 React Native 页面导航的试试标准。



官方默认的使用方式是通过组件的 `navigation` 属性来进行页面切换。所有在路由中注册过的页面，都会被注入这个属性，在组件中可用访问到：



```
// 官方示例
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



这种使用方式，是和组件绑定的，组件之间跳转的代码，必须在组件中，此时工作的很好。但有的时候，我们不止需要在组件中访问导航和切换页面，如网络请求中间层、全局状态变化等，组件之外的情况，也有访问导航的需求。







