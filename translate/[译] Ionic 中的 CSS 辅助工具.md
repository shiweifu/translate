翻译自：[CSS Utilities: Classes for Text/Element Alignment or Modification (ionicframework.com)](https://ionicframework.com/docs/layout/css-utilities)



#### CSS 工具



Ionic 框架提供了一组 CSS 工具类，用于帮助你调整文本，元素占位符或者内外边距。



> 注意
>
> 如果你的应用一开始不是使用 Ionic 框架构建的，那么在 [全局样式表的可选部分](https://ionicframework.com/docs/layout/global-stylesheets#optional)，需要引入一些样式，以正常工作。



#### 文本修改



##### 文本对齐



```
<ion-grid>
  <ion-row>
    <ion-col>
      <div class="ion-text-start">
        <h3>text-start</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
    <ion-col>
      <div class="ion-text-end">
        <h3>text-end</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
    <ion-col>
      <div class="ion-text-center">
        <h3>text-center</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
  </ion-row>
  <ion-row>
    <ion-col>
      <div class="ion-text-justify">
        <h3>text-justify</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
    <ion-col>
      <div class="ion-text-wrap">
        <h3>text-wrap</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
    <ion-col>
      <div class="ion-text-nowrap">
        <h3>text-nowrap</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit.
      </div>
    </ion-col>
  </ion-row>
</ion-grid>
```



| Class               | Style Rule            | 描述                                                         |
| ------------------- | --------------------- | ------------------------------------------------------------ |
| `.ion-text-left`    | `text-align: left`    | 盒模型中的内容居左对齐                                       |
| `.ion-text-right`   | `text-align: right`   | 盒模型中的内容居右对齐                                       |
| `.ion-text-start`   | `text-align: start`   | 如果文本的方向是左起向右，则与左对齐功能一致。如果文本方向是右向左，则与右对齐一致。 |
| `.ion-text-end`     | `text-align: end`     | 与上面的类功能一致，只是方向反了。                           |
| `.ion-text-center`  | `text-align: center`  | 盒模型中的内容，居中对齐                                     |
| `.ion-text-justify` | `text-align: justify` | 盒模型中的文本内容，左右对齐，最后一行除外                   |
| `.ion-text-wrap`    | `white-space: normal` | 空白序列被折叠。内容中的换行符被当作空格处理，必要时换行以填充盒。 |
| `.ion-text-nowrap`  | `white-space: nowrap` | 与正常情况一样，折叠空白，但禁止文本换行                     |

#### Text 变形



```
<ion-grid>
  <ion-row>
    <ion-col>
      <div class="ion-text-uppercase">
        <h3>text-uppercase</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
    <ion-col>
      <div class="ion-text-lowercase">
        <h3>text-lowercase</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
    <ion-col>
      <div class="ion-text-capitalize">
        <h3>text-capitalize</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
  </ion-row>
</ion-grid>
```



| Class                  | Style Rule                   | 描述                             |
| ---------------------- | ---------------------------- | -------------------------------- |
| `.ion-text-uppercase`  | `text-transform: uppercase`  | 强制将所有字符转换为大写         |
| `.ion-text-lowercase`  | `text-transform: lowercase`  | 强制将所有字符转换为小写         |
| `.ion-text-capitalize` | `text-transform: capitalize` | 强制将每个单词的首字母转换为大写 |



#### 响应式文本工具类



上面列出的所有文本类，都支持通过添加额外的类，来适配不同尺寸的屏幕。相较于 `text-`，使用 `text-{breakpoint}-`，可以让类在指定的屏幕尺寸中生效，{breakpoint} 的列表值，可以  [Ionic-尺寸断点](https://ionicframework.com/docs/layout/css-utilities#ionic-breakpoints)。



下方的表格中，展示了一些默认行为，`{modifier}` 可以为以下取值：left`, `right`, `start`, `end`, `center`, `justify`, `wrap`, `nowrap`, `uppercase`, `lowercase`, or `capitalize，如上方定义的一样。



| Class                     | Description                            |
| ------------------------- | -------------------------------------- |
| `.ion-text-{modifier}`    | 作用于所有尺寸的屏幕                   |
| `.ion-text-sm-{modifier}` | 作用于最小尺寸为 `min-width: 576px`。  |
| `.ion-text-md-{modifier}` | 作用于最小尺寸为 `min-width: 768px`。  |
| `.ion-text-lg-{modifier}` | 作用于最小尺寸为 `min-width: 992px`。  |
| `.ion-text-xl-{modifier}` | 作用于最小尺寸为 `min-width: 1200px`。 |



### 元素占位符



#### 浮动元素



通过浮动 CSS 属性，指定元素在容器的左侧或者在右侧，文本和行内元素将环绕它。通过这种方式，元素从网页标准的布局中被取出来，但仍然属于标准布局的一部分，这与绝对定位相反。



```
<ion-grid>
  <ion-row>
    <ion-col class="ion-float-left">
      <div>
        <h3>float-left</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
    <ion-col class="ion-float-right">
      <div>
        <h3>float-right</h3>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed ac vehicula lorem.
      </div>
    </ion-col>
  </ion-row>
</ion-grid>
```



| Class              | Style Rule                     | 描述                                                         |
| ------------------ | ------------------------------ | ------------------------------------------------------------ |
| `.ion-float-left`  | `float: left`                  | 元素将浮动在父容器的左侧                                     |
| `.ion-float-right` | `float: right`                 | 元素浮动在父容器的右侧                                       |
| `.ion-float-start` | `float: left` / `float: right` | 如果方向是由左到右，则表现与 `float-left` 一致。 如果方向是右到左，则显示为 `float-right`。 |
| `.ion-float-end`   | `float: left` / `float: right` | 如果方向是由左到右，则表现与 `float-right 一致。 如果方向是右到左，则显示为` `float-left`。 |



### 元素显示



显示相关的 CSS 属性，决定元素是否被显示。如果元素是被隐藏的，即时元素被包在 DOM 中，它也不会被显示。



```
<ion-grid>
  <ion-row>
    <ion-col class="ion-hide">
      <div>
        <h3>hidden</h3>
        You can't see me.
      </div>
    </ion-col>
    <ion-col>
      <div>
        <h3>not-hidden</h3>
        You can see me!
      </div>
    </ion-col>
  </ion-row>
</ion-grid>
```



| Class       | Style Rule      | 描述         |
| ----------- | --------------- | ------------ |
| `.ion-hide` | `display: none` | 元素将被隐藏 |



### 响应式显示属性



有一组额外的类，基于屏幕的尺寸来响应式的控制元素是否显示。不同于 `.ion-hide` 的对全尺寸屏幕生效，`ion-hide-{breakpoint}-{dir}` 只对某些尺寸的屏幕生效。`{breakpoint` 的内容对应  [Ionic 断点列表](https://ionicframework.com/docs/layout/css-utilities#ionic-breakpoints)，`{dir}` 决定元素是否在 `大于` 或者 `小于`  某个屏幕尺寸下生效。



| Class                | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| `.ion-hide-sm-{dir}` | 当 `min-width: 576px` (`大于`) 或者 `max-width: 576px` (`小于`) 时，样式生效 |
| `.ion-hide-md-{dir}` | 当 `min-width: 768px` (`大于`) 或者 `max-width: 768px` (`小于`)时，样式生效 |
| `.ion-hide-lg-{dir}` | 当  `min-width: 992px` (`大于`) or `max-width: 992px` (`小于`)时，样式生效 |
| `.ion-hide-xl-{dir}` | 当 `min-width: 1200px` (`大于`) or `max-width: 1200px` (`小于`) 时，样式生效 |



### 内容空间

#### 元素内边距



内边距类用于设置元素的内边距。内边距区域，是指元素内容和边框之间的区域。



默认情况下，`padding` 的值被设置为 `16px`，可以通过 `--ion-padding` 变量来进行设置。详情请看 [CSS Variables](https://ionicframework.com/docs/theming/css-variables) 了解更多信息，以及如何改变这些变量的值。



```
<ion-grid>
  <ion-row>
    <ion-col class="ion-padding">
      <div>padding</div>
    </ion-col>
    <ion-col class="ion-padding-top">
      <div>padding-top</div>
    </ion-col>
    <ion-col class="ion-padding-start">
      <div>padding-start</div>
    </ion-col>
    <ion-col class="ion-padding-end">
      <div>padding-end</div>
    </ion-col>
  </ion-row>
  <ion-row>
    <ion-col class="ion-padding-bottom">
      <div>padding-bottom</div>
    </ion-col>
    <ion-col class="ion-padding-vertical">
      <div>padding-vertical</div>
    </ion-col>
    <ion-col class="ion-padding-horizontal">
      <div>padding-horizontal</div>
    </ion-col>
    <ion-col class="ion-no-padding">
      <div>no-padding</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



| Class                     | Style Rule             | 描述               |
| ------------------------- | ---------------------- | ------------------ |
| `.ion-padding`            | `padding: 16px         | 对所有边都生效     |
| `.ion-padding-top`        | `padding-top: 16px`    | 对顶部生效         |
| `.ion-padding-start`      | `padding-start: 16px`  | 对开始的方向生效   |
| `.ion-padding-end`        | `padding-end: 16px`    | 对末尾的方向生效   |
| `.ion-padding-bottom`     | `padding-bottom: 16px` | 对底部生效         |
| `.ion-padding-vertical`   | `padding: 16px 0`      | 对顶部或者底部生效 |
| `.ion-padding-horizontal` | `padding: 0 16px`      | 对左右生效         |
| `.ion-no-padding`         | `padding: 0`           | 不要内边距         |



### 元素外边距



外边距区域是只边框之外的区域，用于分隔元素与它的邻居元素。



默认情况下，`margin` 的值为 `16px`，通过 `--ion-margin` 变量来设置。查看 [CSS 变量](https://ionicframework.com/docs/theming/css-variables) 章节，了解更多信息。



```
<ion-grid>
  <ion-row>
    <ion-col class="ion-margin">
      <div>margin</div>
    </ion-col>
    <ion-col class="ion-margin-top">
      <div>margin-top</div>
    </ion-col>
    <ion-col class="ion-margin-start">
      <div>margin-start</div>
    </ion-col>
    <ion-col class="ion-margin-end">
      <div>margin-end</div>
    </ion-col>
  </ion-row>
  <ion-row>
    <ion-col class="ion-margin-bottom">
      <div>margin-bottom</div>
    </ion-col>
    <ion-col class="ion-margin-vertical">
      <div>margin-vertical</div>
    </ion-col>
    <ion-col class="ion-margin-horizontal">
      <div>margin-horizontal</div>
    </ion-col>
    <ion-col class="ion-no-margin">
      <div>no-margin</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



| Class                    | Style Rule            | 描述                                 |
| ------------------------ | --------------------- | ------------------------------------ |
| `.ion-margin`            | `margin: 16px`        | 应用于元素所有的边。                 |
| `.ion-margin-top`        | `margin-top: 16px`    | 作用于元素的顶边。                   |
| `.ion-margin-start`      | `margin-start: 16px`  | 增加外边距于元素的左边。             |
| `.ion-margin-end`        | `margin-end: 16px`    | 增加外边距于元素的右边。             |
| `.ion-margin-bottom`     | `margin-bottom: 16px` | 增加外边距于元素的底边。             |
| `.ion-margin-vertical`   | `margin: 16px 0`      | 增加外边距于元素的元素的顶边和底边。 |
| `.ion-margin-horizontal` | `margin: 0 16px`      | 增加外边距于元素的左边和右边。       |
| `.ion-no-margin`         | `margin: 0`           | 元素所有边都没有外边距。             |



## Flex 属性



#### Flex 容器属性



```
<ion-grid>
  <ion-row class="ion-justify-content-start">
    <ion-col size="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-justify-content-center">
    <ion-col size="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-justify-content-end">
    <ion-col size="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-justify-content-around">
    <ion-col size="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-justify-content-between">
    <ion-col size="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-justify-content-evenly">
    <ion-col size="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>
</ion-grid>

<ion-grid>
  <ion-row class="ion-align-items-start">
    <ion-col>
      <div>1 of 4</div>
    </ion-col>
    <ion-col>
      <div>2 of 4</div>
    </ion-col>
    <ion-col>
      <div>3 of 4</div>
    </ion-col>
    <ion-col>
      <div>4 of 4 # # #</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-align-items-end">
    <ion-col>
      <div>1 of 4</div>
    </ion-col>
    <ion-col>
      <div>2 of 4</div>
    </ion-col>
    <ion-col>
      <div>3 of 4</div>
    </ion-col>
    <ion-col>
      <div>4 of 4 # # #</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-align-items-center">
    <ion-col>
      <div>1 of 4</div>
    </ion-col>
    <ion-col>
      <div>2 of 4</div>
    </ion-col>
    <ion-col>
      <div>3 of 4</div>
    </ion-col>
    <ion-col>
      <div>4 of 4 # # #</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-align-items-baseline">
    <ion-col>
      <div>1 of 4</div>
    </ion-col>
    <ion-col>
      <div>2 of 4</div>
    </ion-col>
    <ion-col>
      <div>3 of 4</div>
    </ion-col>
    <ion-col>
      <div>4 of 4 # # #</div>
    </ion-col>
  </ion-row>

  <ion-row class="ion-align-items-stretch">
    <ion-col>
      <div>1 of 4</div>
    </ion-col>
    <ion-col>
      <div>2 of 4</div>
    </ion-col>
    <ion-col>
      <div>3 of 4</div>
    </ion-col>
    <ion-col>
      <div>4 of 4 # # #</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



| Class                          | Style Rule                       | 描述                                             |
| ------------------------------ | -------------------------------- | ------------------------------------------------ |
| `.ion-justify-content-start`   | `justify-content: flex-start`    | 元素以主轴的起始位置对齐                         |
| `.ion-justify-content-end`     | `justify-content: flex-end`      | 元素以主轴的末尾位置对齐                         |
| `.ion-justify-content-center`  | `justify-content: center`        | 元素在主轴的中心位置对齐                         |
| `.ion-justify-content-around`  | `justify-content: space-around`  | 条目均匀分布在主轴上，左右的空间相等             |
| `.ion-justify-content-between` | `justify-content: space-between` | 元素均匀的分布在主轴上                           |
| `.ion-justify-content-evenly`  | `justify-content: space-evenly`  | 元素均匀分布在主轴上，任意两个元素之间的距离相等 |
| `.ion-align-items-start`       | `align-items: flex-start`        | 元素在纵轴起始位置对齐                           |
| `.ion-align-items-end`         | `align-items: flex-end`          | 元素在纵轴末尾位置对齐                           |
| `.ion-align-items-center`      | `align-items: center`            | 元素在纵轴上居中                                 |
| `.ion-align-items-baseline`    | `align-items: baseline`          | 元素在纵轴的基线上对齐                           |
| `.ion-align-items-stretch`     | `align-items: stretch`           | 拉伸元素以填满父组件                             |
| `.ion-nowrap`                  | `flex-wrap: nowrap`              | 元素溢出后，不换行                               |
| `.ion-wrap`                    | `flex-wrap: wrap`                | 元素溢出后，换行                                 |
| `.ion-wrap-reverse`            | `flex-wrap: wrap-reverse`        | 元素溢出后，从下到上换行                         |

