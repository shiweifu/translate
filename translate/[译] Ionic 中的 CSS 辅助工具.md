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



