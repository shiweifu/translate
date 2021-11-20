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



