翻译自：[Global Stylesheet: Styled CSS Component Options for Ionic Apps (ionicframework.com)](https://ionicframework.com/docs/layout/global-stylesheets)



### 全局样式



虽然 Ionic 框架组件的样式是独立的，但为了使用全部特性，应该包含几个全局样式表。为了使 Ionic 的外观和行为正确，需要使用一些样式表。其他可选的引用样式，可以更快速的开发应用程序。



### 当前可用



#### 必备



必须引用以下 CSS 文件，Ionic 才能正确工作。



#### core.css



该文件只包含 Ionic 框架中组件所必备的样式。如果该文件没有被引用，一些组件将失去样式。如果 Ionic 框架应用在非 App 环境下，该文件并非必须。



#### structure.css



将样式添加到 `<html>` 元素中，默认的 `box-sizing` 设置为 `border-box`。它确保滚动行为如移动设备一致。



#### typography.css



Typography 改变整个文档的 `font-family`，以及修改标题元素的  font style。它也会将样式，应用于一些本地文本元素。



#### normalize.css



使浏览器成员的所有元素，更加一致和符合现代标准。它基于 `Normalize.css`。



### 可选



以下一组CSS文件是可选的，如果应用程序不使用任何特性，可以安全地注释掉或删除它们。



#### padding.css



将工具类，添加到元素中，修改元素的内边距或者外边距，查看 [CSS Utilities](https://ionicframework.com/docs/layout/css-utilities#content-space)  了解更多信息。



#### float-elements.css



基于媒体查询断点，将工具类添加到元素，查看 [CSS Utilities](https://ionicframework.com/docs/layout/css-utilities#content-space)  了解更多信息。











