React Native 笔记1 - 网络请求调试



React Native 代码直接运行在模拟器上，无法简单的像 React 一样，在浏览器的开发者工具中查看网络请求的信息。



传统的做法是通过 Charles 这类的抓包工具来捕获网络请求，然后进行调试和分析。这种做法好处是抓取的信息比较完整，可以精确的分析请求，缺点是比较繁琐，没有针对性，展示的信息过于 raw，还得再处理一波。



如果你的需求比较简单，只是想看看请求调用的情况，那么你可以使用 [Reactotron](https://github.com/infinitered/reactotron) 进行调试，这是一个桌面应用，专门为调试 React App 而设计。除了网络请求，甚至还能调试 React 的状态组件。



#### 安装



Reactotron 安装分为两部分：分别为桌面应用程序和 React 不同平台的 npm 包。



桌面应用程序直接在 Github Release 页面下载：



[Release v2.17.1 · infinitered/reactotron (github.com)](https://github.com/infinitered/reactotron/releases/tag/v2.17.1)



页面有各种平台供下载，最后 Release 的日期是 2019 年，有点久了，使用没问题。



然后是安装 npm 包。



```
npm i --save-dev reactotron-react-native
```



安装完毕后，官方文档给出了好几种集成方式，主要目的是让 npm 包正确加载。这里使用最简单的一种：建立配置文件，在 App 文件中引用：



首先，在项目根目录下建立 `ReactotronConfig.js` 文件，并写入内容：



```
import Reactotron from 'reactotron-react-native'

Reactotron
  .setAsyncStorageHandler(AsyncStorage) // AsyncStorage would either come from `react-native` or `@react-native-community/async-storage` depending on where you get it from
  .configure() // controls connection & communication settings
  .useReactNative() // add all built-in react native plugins
  .connect() // let's connect!
```









