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

te'r