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



现在，如果我们将所有我们编写的代码放在一起，它们是这样的：



```
import React from 'react';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { NavigationContainer } from '@react-navigation/native';
import All from './screens/All';
import Business from './screens/Business';
import HealthScreen from './screens/Health';
import SportsScreen from './screens/Sports';
import TechScreen from './screens/Tech';
const Tab = createBottomTabNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator>
        <Tab.Screen name="All" component={All} />
        <Tab.Screen name="Business" component={Business} />
        <Tab.Screen name="Health" component={HealthScreen} />
        <Tab.Screen name="Sports" component={SportsScreen} />
        <Tab.Screen name="Tech" component={TechScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
} 
```





这些代码对应如下界面：



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-181356.png)



我们一共有五个页面，分别是：All，Business，Health，Sports，Tech。



现在，我们做一些调整。我们需要为底部菜单来换一下图标。



首先，我们需要加载图标库。我们使用 `react-native-elements`。



使用下面的命令来安装：



```
npm install react-native-elements
```



这个库提供了一些流行的图标来供我们使用。



现在，我们来添加图标到底部菜单栏。



```
<Tab.Screen name="All" component={All}
          options={{
            tabBarIcon: (props) => (
              <Icon type='feather' name='home' color={props.color} />
            ),
          }} />
```



这里我们添加了名为 `home` 的图标，到 `home` 页面，并指定使用 `feather` 库中的图标。



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-194136.png)



上述图片即为此时的输出。与之类似，让我们来为其他 tab，增加图标。



```
<Tab.Navigator>
        <Tab.Screen name="All" component={All}
          options={{
            tabBarIcon: (props) => (
              <Icon type='feather' name='home' color={props.color} />
            ),
          }} />

        <Tab.Screen name="Business" component={Business}
          options={{
            tabBarIcon: (props) => (
              <Icon type='feather' name='dollar-sign' color={props.color} />
            ),
          }} />

        <Tab.Screen name="Health" component={HealthScreen}
          options={{
            tabBarIcon: (props) => (
              <Icon type='feather' name='heart' color={props.color} />
            ),
          }} />

        <Tab.Screen name="Sports" component={SportsScreen}
          options={{
            tabBarIcon: (props) => (
              <Icon type='ionicon' name="tennisball-outline" color={props.color} />
            ),
          }} />

        <Tab.Screen name="Tech" component={TechScreen}
          options={{
            tabBarIcon: (props) => (
              <Icon type='ionicon' name="hardware-chip-outline" color={props.color} />
            ),
          }} />
      </Tab.Navigator>
```



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-194525.png)



现在，每个页面都已经添加完毕，它们的图标也已经正确。



### 如何调用新闻 API



现在，我们来调用 `http://newsapi.org`。



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-194845.png)



访问网站并注册。它将会分配给你一个 API。



我们需要一个配置文件，存储全部的新闻内容，我们来创建它。



```
export const API_KEY = ``;
export const endpoint = `https://newsapi.org/v2/top-headlines`;
export const country = 'in'
```



我们需要 API_KEY，访问端点，以及区域代码。



现在，我们需要创建我们的服务，来调用 GET API 请求。



从 `services.js` 发起调用。



这里，引入 API_KEY，端点，以及城市在文件顶部。



```
import { API_KEY, endpoint, country } from '../config/config';
```



然后，我们来编写服务的内容。



```
export async function services(category = 'general') {
    let articles = await fetch(`${endpoint}?country=${country}&category=${category}`, {
        headers: {
            'X-API-KEY': API_KEY
        }
    });

    let result = await articles.json();
    articles = null;

    return result.articles;
}
```



因此，我们通过使用端点获取新闻数据，并附加国家和类别。在该函数中，我们将类别作为一般类别传递，因为这是默认类别。我们还在头文件中传递API密钥。



然后，我们将响应或传入数据转换为JSON格式，并将其存储在结果变量中。



以下是全部代码内容，可以参考：



```
import { API_KEY, endpoint, country } from '../config/config';

export async function services(category = 'general') {
    let articles = await fetch(`${endpoint}?country=${country}&category=${category}`, {
        headers: {
            'X-API-KEY': API_KEY
        }
    });

    let result = await articles.json();
    articles = null;

    return result.articles;
}
```



现在，我们需要在 All.js 文件中，引入这个服务文件。



```
import { services } from '../services/services';
```



我们需要使用useState和useEffect钩子。useEffect钩子会在All.js文件中调用这个服务，useState会创建一个状态来存储来自API的响应。



```
import React, { useEffect, useState } from 'react'
import { View } from 'react-native';
import { services } from '../services/services';
export default function All() {
    const [newsData, setNewsData] = useState([])
    useEffect(() => {
        services('general')
            .then(data => {
                setNewsData(data)
            })
            .catch(error => {
                alert(error)
            })
    }, [])
    return (
        <View>

        </View>
    )
}
```



在这个文件中，我们调用useEffect钩子中的服务。然后我们将响应存储在newsData状态中，这是一个数组。我们还为类别传递一个参数，这是一般的。



这个屏幕将获取所有的新闻，所以我们使用一般类别。每隔一个屏幕它就会改变。它将是“健康”屏幕上的“健康”，“运动”屏幕上的“运动”，等等。



现在，我们需要在接口中显示这些数据。为此，我们还需要另一个名为Native Base的包。所以，让我们安装它。



键入下面的命令来安装Native Base



```
yarn add native-base styled-components styled-system
expo install react-native-svg react-native-safe-area-context
```



在All.js中，让我们从Native Base中导入一些东西



```
import React, { useEffect, useState } from 'react'
import { View, Text } from 'react-native';
import { NativeBaseProvider, FlatList, ScrollView, Divider, Image, Spinner } from 'native-base';
import { services } from '../services/services';
```



然后，在返回语句，我们添加 `NativeBaseProvider`。

```
return (
        <NativeBaseProvider>
            
        </NativeBaseProvider>
    )
```



然后，我们添加 ScrollView。当新闻数据超出屏幕高度时，用户可以向下滑动。



```
<NativeBaseProvider>
            <ScrollView height={850}>

            </ScrollView>
        </NativeBaseProvider>
```



现在，让我们添加 `FlatList` 来展示我们的新闻数据。



```
<NativeBaseProvider>
            <ScrollView height={850}>
                <FlatList
                    data={newsData}
                    renderItem={({ item }) => (
                       <View>

                       </View> 
                    )}
                    keyExtractor={(item) => item.id}
                />
            </ScrollView>
        </NativeBaseProvider>
```



FlatList 接受一个数据属性，这是我们之前创建的 newsData 的状态，它通过 renderItems 返回一个项目。



这就像 JavaScript 中的 `map`，他处理数组中的每一个数据，并返回结果。通过 `keyExtractor`，我们来保证 item 唯一。



现在，我们来在视图中看我们的数据。



在父视图中，创建更多的视图：



```
<NativeBaseProvider>
            <ScrollView height={850}>
                <FlatList
                    data={newsData}
                    renderItem={({ item }) => (
                       <View>
                           <View>
                               
                           </View>
                       </View> 
                    )}
                    keyExtractor={(item) => item.id}
                />
            </ScrollView>
        </NativeBaseProvider>
```



添加一些文本到子视图：



```
<NativeBaseProvider>
            <ScrollView height={850}>
                <FlatList
                    data={newsData}
                    renderItem={({ item }) => (
                        <View>
                            <View>
                                <Text>
                                    {item.title}
                                </Text>
                                <Text>
                                    {item.publishedAt}
                                </Text>
                                <Text>
                                    {item.description}
                                </Text>
                            </View>
                        </View>
                    )}
                    keyExtractor={(item) => item.id}
                />
            </ScrollView>
        </NativeBaseProvider>
```



在我们的样式中，为 spinner 控件添加下面的样式代码。



```
spinner: {
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        height: 400
}
```



下面是到目前为止的完整代码：



```
import React, { useEffect, useState } from 'react'
import { View, Text, StyleSheet } from 'react-native';
import { NativeBaseProvider, FlatList, ScrollView, Divider, Image, Spinner } from 'native-base';
import { services } from '../services/services';
import moment from 'moment'
export default function All() {
    const [newsData, setNewsData] = useState([])
    useEffect(() => {
        services('general')
            .then(data => {
                setNewsData(data)
            })
            .catch(error => {
                alert(error)
            })
    }, [])
    return (
        <NativeBaseProvider>
            <ScrollView height={850}>
                {newsData.length > 1 ? (
                    <FlatList
                        data={newsData}
                        renderItem={({ item }) => (
                            <View>
                                <View style={styles.newsContainer}>
                                    <Image
                                        width={550}
                                        height={250}
                                        resizeMode={"cover"}
                                        source={{
                                            uri: item.urlToImage,
                                        }}
                                        alt="Alternate Text"
                                    />
                                    <Text style={styles.title}>
                                        {item.title}
                                    </Text>
                                    <Text style={styles.date}>
                                        {moment(item.publishedAt).format('LLL')}
                                    </Text>
                                    <Text style={styles.newsDescription}>
                                        {item.description}
                                    </Text>
                                </View>
                                <Divider my={2} bg="#e0e0e0" />
                            </View>
                        )}
                        keyExtractor={(item) => item.id}
                    />
                ) : (
                    <View style={styles.spinner}>
                        <Spinner color="danger.400" />
                    </View>
                )}
            </ScrollView>
        </NativeBaseProvider>
    )
}

const styles = StyleSheet.create({
    newsContainer: {
        padding: 10
    },
    title: {
        fontSize: 18,
        marginTop: 10,
        fontWeight: "600"
    },
    newsDescription: {
        fontSize: 16,
        marginTop: 10
    },
    date: {
        fontSize: 14
    },
    spinner: {
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        height: 400
    }
});
```



至此，我们的 All.js 页面已全部完成。



![img](https://www.freecodecamp.org/news/content/images/2021/08/Screenshot-2021-08-21-210200.png)



向下滚动业务屏幕，您将看到与业务相关的新闻。



你可以对所有其他屏幕做同样的事情。



```
useEffect(() => {
        services('business')
            .then(data => {
                setNewsData(data)
            })
            .catch(error => {
                alert(error)
            })
}, [])
```



```
useEffect(() => {
        services('health')
            .then(data => {
                setNewsData(data)
            })
            .catch(error => {
                alert(error)
            })
    }, [])
```



```
useEffect(() => {
        services('sports')
            .then(data => {
                setNewsData(data)
            })
            .catch(error => {
                alert(error)
            })
    }, [])
```



```
useEffect(() => {
        services('technology')
            .then(data => {
                setNewsData(data)
            })
            .catch(error => {
                alert(error)
            })
    }, [])
```



### 结尾



现在祝贺您！此时我们的新闻应用已经全部完成。



所以继续完善它吧，构建并尝试一下它。还有很多事情可以做。



你可以在我的YouTube频道上 [用React Native和Native Base构建新闻应用](https://youtube.com/playlist?list=PLWgH1O_994O8KP1srByN1OmoiRpLF6lNO) 的播放列表上查看关于本教程的视频版。



您也可以随时下载代码：https://github.com/nishant-666/reacor-native-news



最后，感谢您的学习，希望学习快乐。

