翻译自：[How to Build an Android News App with React Native and Native Base (freecodecamp.org)](https://www.freecodecamp.org/news/build-an-android-news-app-with-react-native-and-native-base/)



我们生活的世界一切都在不停变化。为了能及时了解最新发生的事情，我们需要一个好的新闻应用。



为了帮助你学习一些很酷的技术并紧跟发展，在本文中，我们将使用 React Native 技术，开发一个 Android 平台的新闻应用程序。它将从不同的新闻频道获取标题，并按类别显示它们。



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-210544.png)



这就是这个应用运行起来的样子。让我们一步一步深入它。



### 如何安装 Expo

那么，什么是 Expo 呢？ Expo 是一个框架，帮助你快速构建和分发 React Native App。



让我们安装它。



```
npm install --global expo-cli
```



在终端上运行这个命令来安装 Expo，--global 参数的意思是全局安装。



安装完毕后，我们需要创建 Expo 项目。

```
expo init News-Application
```



使用上面的命令初始化项目。它会问你一些问题，比如你的应用程序的名字，你是否想要在你的项目中添加TypeScript，或者从一个空白的项目开始。只需选择空白，并按回车键。



然后，它将下载文件夹中的所有包和依赖项。



现在，完成之后，导航到项目文件夹。要启动应用程序，请键入expo start。它将在浏览器中打开开发工具。



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-174505.png)



在这里，您将在左侧看到许多选项，如在Android设备/模拟器上运行，也可以在iOS模拟器上运行。我们将在Web浏览器上运行应用程序，因此单击Web浏览器选项中的运行。



```
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text>Open up App.js to start working on your app!</Text>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```



这是我们的 App.js 文件，它包含一些默认的样式。



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-175022.png)



现在，我们的应用运行起来了。



### 如何使用 React 导航创建不同的屏幕

现在，我们来创建不同的应用。为了实现这个目标，我们要使用 React Navigation。我们首先来安装它。



访问 https://reactnavigation.org/，点击阅读文档。此时将打开文档页。



使用下面的命令来安装它：

```
npm install @react-navigation/native

expo install react-native-screens react-native-safe-area-context
```



现在，在我们的环境中，React Navigation 已经安装好了。



接下来，我们使用 `bottomTabNavigator`。从页面的左侧菜单，选择 API 手册，然后点击 Navigators，然后点击 Bottom Tabs。



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-175641.png)



使用下面的命令安装 Bottom Tabs：

```
npm install @react-navigation/bottom-tabs
```



现在，在我们的 `App.js` 文件，我们需要引入 Bottom Tabs，然后使用它。



使用下面的命令引入：

```
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
```



现在，让我们引入 Tab Screen。

```
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { NavigationContainer } from '@react-navigation/native';
const Tab = createBottomTabNavigator();

function MyTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="Settings" component={SettingsScreen} />
    </Tab.Navigator>
  );
}
```



这是如何创建 Bottom Tabs。



在我们的例子中，我们需要做一些事情：



```
<Tab.Navigator>
  <Tab.Screen name="All" component={All} />
  <Tab.Screen name="Business" component={Business} />
  <Tab.Screen name="Health" component={HealthScreen} />
  <Tab.Screen name="Sports" component={SportsScreen} />
  <Tab.Screen name="Tech" component={TechScreen} />
</Tab.Navigator>
```



接着我们需要创建一些页面：全部新闻页，商业新闻页，运动新闻页，健康新闻页，以及科技新闻页。当然也需要为每个页面创建一个共同的组件。



我们需要封装 `TabNavigtor` 在 `NavigationContainer` 向下面这样：



```
<NavigationContainer>
  <Tab.Navigator>
    <Tab.Screen name="All" component={All} />
    <Tab.Screen name="Business" component={Business} />
    <Tab.Screen name="Health" component={HealthScreen} />
    <Tab.Screen name="Sports" component={SportsScreen} />
    <Tab.Screen name="Tech" component={TechScreen} />
  </Tab.Navigator>
</NavigationContainer>
```



我们同样需要引入那些组件，我们可以在文件顶部操作。



```
import All from './screens/All';
import Business from './screens/Business';
import HealthScreen from './screens/Health';
import SportsScreen from './screens/Sports';
import TechScreen from './screens/Tech';
```

