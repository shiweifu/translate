翻译自：[Responsive Grid System and Column Layout Based on Screen Size (ionicframework.com)](https://ionicframework.com/docs/layout/grid)



### 响应式网格



网格系统是一个强大的，移动优先，基于 Flex 的系统，用于构建自定义布局。由三个单元组成，一个[grid](https://ionicframework.com/docs/api/grid)，[row(s)](https://ionicframework.com/docs/api/row)，和 [column(s)](https://ionicframework.com/docs/api/col)。Columns 将扩展以填充其行，并将调整大小，适应其他列。他基于屏幕大小的 12 列布局，该布局根据屏幕大小，具有不同的断点，列数可以通过 CSS 来定制。



#### 如何工作



```
<ion-grid>
  <ion-row>
    <ion-col>
      <div>1 of 3</div>
    </ion-col>
    <ion-col>
      <div>2 of 3</div>
    </ion-col>
    <ion-col>
      <div>3 of 3</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



- 网格充当所有行和列的容器。网格占据容器的全部宽度，但是添加 `fixed` 属性将指定每个屏幕大小的宽度，参见下面的[网格大小](https://ionicframework.com/docs/layout/grid#grid-size) 。

- 行是将列正确排列的水平列组。
- 内容将被放置在列中，只有列可以是行的子列
- `size-{breakpoint}` 属性标识使用怎样的比例看待一行，默认是分为 12 列。所以，`size="4"` 意味着一行的 1/3 内容，也可以说是 4/12。
- 列没有设置值，默认将使用相同的宽度。举个例子，四个实例，都采用 `size-sm`，将被分为四等份。
- 列宽度按百分比设置，始终是流动的，大小与父元素有关。
- 每个列都有自己独立的内边距，因此，也可以向列添加 `ion-no-padding`，来删除内边距。详情请查看  [CSS Utilities](https://ionicframework.com/docs/layout/css-utilities)，来了解有关样式设置内容中的网格部分。
- 有五个网格层，每个响应断点对应一个：所有断点（超小），小，中等，大和超大。
- 网格层基于最小宽度，这意味着它们适用于所有的层，以及比它们尺寸大的设备（例如：size-sm="4"，适用于小型、中型、大型和超大型设备）。
- 网格可以使用 CSS 变量轻松的定制，更多信息请查看 [定制网格](https://ionicframework.com/docs/layout/grid#customizing-the-grid)。



#### 实时案例



你可以查看 Angular 版本的 [案例](https://stackblitz.com/edit/ionic-ng-basic-grid)，或者 React 版本的 [案例](https://stackblitz.com/edit/ionic-react-basic-grid)。



### 网格大小



默认情况下，网格占据 100% 宽度。要基于屏幕尺寸来设置宽度，请添加 `fixed` 属性。指定网格每个断点的宽度，通过 `ion-grid-width-{breakpoint}` 定义 CSS 变量来设置。更多信息，请查看 [自定义网格](https://ionicframework.com/docs/layout/grid#customizing-the-grid)。



| 名字 | 值     | 描述                                          |
| ---- | ------ | --------------------------------------------- |
| xs   | 100%   | 在非常小的屏幕中，不设置网格宽度              |
| sm   | 540px  | 最小宽度为 576px 时候，设置网格宽度为 540px   |
| md   | 720px  | 最小宽度为 768px 时候，设置网格宽度为 720px   |
| lg   | 960px  | 最小宽度为 992px 时候，设置网格宽度为 960px   |
| xl   | 1140px | 最小宽度为 1200px 时候，设置网格宽度为 1140px |



### 实时案例



你可以查看实时案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-fixed-width-grid)，React 版本查看 [这里](https://stackblitz.com/edit/ionic-react-fixed-width-grid)。



### 网格属性



默认情况下，网格会占据整个屏幕的宽度。这可以使用下面的属性进行修改。



| 属性  | 描述                       |
| ----- | -------------------------- |
| fixed | 设置基于当前屏幕的最大宽度 |



### 默认断点



默认断点在下表中定义。此时不能自定义断点。有关为什么不能自定义它们的更多信息，请参见[媒体查询中的变量](https://ionicframework.com/docs/theming/advanced#variables-in-media-queries)。



| 名称 | 值     | Width Prefix | Offset Prefix | Push Prefix | Pull Prefix | 描述                            |
| ---- | ------ | ------------ | ------------- | ----------- | ----------- | ------------------------------- |
| xs   | 0      | `size-`      | `offset-`     | `push-`     | `pull-`     | 当 min-width: 0 时，设置列      |
| sm   | 576px  | `size-sm-`   | `offset-sm-`  | `push-sm-`  | `pull-sm-`  | 当 min-width: 576px 时，设置列  |
| md   | 768px  | `size-md-`   | `offset-md-`  | `push-md-`  | `pull-md-`  | 当 min-width: 768px 时，设置列  |
| lg   | 992px  | `size-lg-`   | `offset-lg-`  | `push-lg-`  | `pull-lg-`  | 当 min-width: 992px 时，设置列  |
| xl   | 1200px | `size-xl-`   | `offset-xl-`  | `push-xl-`  | `pull-xl-`  | 当 min-width: 1200px 时，设置列 |



### 自动布局列



#### 等宽



默认情况下，对于所有设备和屏幕大小，列将在一行内，占用相等的宽度。



```
<ion-grid>
  <ion-row>
    <ion-col>
      <div>1 of 2</div>
    </ion-col>
    <ion-col>
      <div>2 of 2</div>
    </ion-col>
  </ion-row>
  <ion-row>
    <ion-col>
      <div>1 of 3</div>
    </ion-col>
    <ion-col>
      <div>2 of 3</div>
    </ion-col>
    <ion-col>
      <div>3 of 3</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



#### 设置一列宽度



设置一列的宽度，其他列将自动调整大小。这可以使用我们的预定义网格属性来完成。下面示例中，无论中心列的宽度如何，其他列将调整大小。



```
<ion-grid>
  <ion-row>
    <ion-col>
      <div>1 of 3</div>
    </ion-col>
    <ion-col size="8">
      <div>2 of 3 (wider)</div>
    </ion-col>
    <ion-col>
      <div>3 of 3</div>
    </ion-col>
  </ion-row>
  <ion-row>
    <ion-col>
      <div>1 of 3</div>
    </ion-col>
    <ion-col size="6">
      <div>2 of 3 (wider)</div>
    </ion-col>
    <ion-col>
      <div>3 of 3</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



#### 实时案例



你可以查看实时案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-set-width-col)，React 版本查看 [这里](https://stackblitz.com/edit/ionic-react-set-width-col)。



#### 可变宽度



通过设置 `size-{breakpoint}` 属性为 `"auto"`，列可以根据其内容自然宽度，自动调整大小。这对于使用像素设置列宽，非常有用。可变宽度列旁边的列，将调整大小，以填充行。



```
<ion-grid>
  <ion-row>
    <ion-col>
      <div>1 of 3</div>
    </ion-col>
    <ion-col size="auto">
      <div>Variable width content</div>
    </ion-col>
    <ion-col>
      <div>3 of 3</div>
    </ion-col>
  </ion-row>
  <ion-row>
    <ion-col>
      <div>1 of 4</div>
    </ion-col>
    <ion-col>
      <div>2 of 4</div>
    </ion-col>
    <ion-col size="auto">
      <div>
        <ion-input placeholder="Variable width input"></ion-input>
      </div>
    </ion-col>
    <ion-col>
      <div>4 of 4</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



#### 实时案例



你可以查看实时案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-var-width-col) ，React 版本查看 [这里](https://stackblitz.com/edit/ionic-react-var-width-col)。



### 响应属性



#### 所有断点



要为所有设备和屏幕定制列的宽度，请设置 `size` 属性。此属性的值确定该列应占可用列总数的多少列。



```
<ion-grid>
  <ion-row>
    <ion-col size="4">
      <div>1 of 4</div>
    </ion-col>
    <ion-col size="2">
      <div>2 of 4</div>
    </ion-col>
    <ion-col size="2">
      <div>3 of 4</div>
    </ion-col>
    <ion-col size="4">
      <div>4 of 4</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



### 水平堆放



使用宽度和断点属性的组合来创建一个网格，它在小屏幕上开始堆叠，然后在小屏幕上变成水平的。



```
<ion-grid>
  <ion-row>
    <ion-col size="12" size-sm>
      <div>1 of 4</div>
    </ion-col>
    <ion-col size="12" size-sm>
      <div>2 of 4</div>
    </ion-col>
    <ion-col size="12" size-sm>
      <div>3 of 4</div>
    </ion-col>
    <ion-col size="12" size-sm>
      <div>4 of 4</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



#### 实时案例



你可以查看实时案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-stacked-horizontal-grid) ，React 版本查看 [这里](https://stackblitz.com/edit/ionic-react-stacked-horizontal-grid)。



### 重新排序



#### 列边距



通过添加 offset 属性向右移动列。此属性将列的左边距增加指定列的数目。例如，在下面的网格中，最后一列将偏移3列并占用3列。



```
<ion-grid>
  <ion-row>
    <ion-col size="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3" offset="3">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



偏移量也可以基于屏幕断点添加。下面是一个网格的例子，在`md`屏幕上，最后一列将被偏移3列



```
<ion-grid>
  <ion-row>
    <ion-col size-md="3">
      <div>1 of 3</div>
    </ion-col>
    <ion-col size-md="3">
      <div>2 of 3</div>
    </ion-col>
    <ion-col size-md="3" offset-md="3">
      <div>3 of 3</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



你可以查看实时案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-offset-grid-cols) ，React 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-offset-grid-cols) 。



### 推和拉



通过添加推和拉属性来重新排序列。这些属性可以根据指定的列数调整列的左右，从而方便对列进行重新排序。例如，在下面的网格中，描述为2中的1的列实际上是最后一列，而描述为2中的2的列将是第一列。



```
<ion-grid>
  <ion-row>
    <ion-col size="9" push="3">
      <div>1 of 2</div>
    </ion-col>
    <ion-col size="3" pull="9">
      <div>2 of 2</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



推和拉也可以基于屏幕断点添加。在下面的例子中，包含`3 / 3`列描述的列实际上是`md`屏幕上的第一列。



```
<ion-grid>
  <ion-row>
    <ion-col size-md="6" push-md="3">
      <div>1 of 3</div>
    </ion-col>
    <ion-col size-md="3" push-md="3">
      <div>2 of 3</div>
    </ion-col>
    <ion-col size-md="3" pull-md="9">
      <div>3 of 3</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



你可以查看实时案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-grid-push-pull) ，React 版本查看 [这里](https://stackblitz.com/edit/ionic-react-grid-push-pull) 。



### 对齐



#### 垂直对齐



通过向行中添加不同的类，所有列都可以在行内部垂直对齐。有关可用类的列表，请参阅[css 实用工具](https://ionicframework.com/docs/layout/css-utilities#flex-container-properties)。



```
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
</ion-grid>
```



通过直接向列添加对齐类，列自身的对齐方式也可以与其他列不同。有关可用类的列表，请参考：[CSS 实用工具](https://ionicframework.com/docs/layout/css-utilities#flex-item-properties)。



```
<ion-grid>
  <ion-row>
    <ion-col class="ion-align-self-start">
      <div>1 of 4</div>
    </ion-col>
    <ion-col class="ion-align-self-center">
      <div>2 of 4</div>
    </ion-col>
    <ion-col class="ion-align-self-end">
      <div>3 of 4</div>
    </ion-col>
    <ion-col>
      <div>4 of 4 # # #</div>
    </ion-col>
  </ion-row>
</ion-grid>
```



#### 在线案例



你可以查看在线案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-grid-vertical-align) ，React 版本查看 [这里](https://stackblitz.com/edit/ionic-react-grid-vertical-align) 。



### 水平对齐



通过向行中，添加不同的类，所有列都可以在行内，水平对齐。有关可用的类，请查阅  [CSS 实用工具](https://ionicframework.com/docs/layout/css-utilities#flex-container-properties)。



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
</ion-grid>
```



#### 在线案例



你可以查看在线案例，Angular 版本查看 [这里](https://stackblitz.com/edit/ionic-ng-grid-horizontal-align) ，React 版本查看 [这里](https://stackblitz.com/edit/ionic-react-grid-horizontal-align) 。



#### 自定义网格



使用我们内置的CSS变量，可以定制预定义的网格属性。更改填充的值、列数等。



#### 网格数量



可以使用 `ion-grid-columns` CSS变量修改网格列的数量。默认情况下，有12个网格列，但这可以更改为任何正整数，并用于计算每一列的宽度。



```
--ion-grid-columns: 12;
```



#### 网格边距



网格容器边距，在一切查询断点下，都可以通过使用 `--ion-grid-padding` CSS 变量进行设置。如果要覆盖独立的断点，使用 `--ion-grid-padding-{breakpoint}` CSS 变量进行设置。



```
--ion-grid-padding: 5px;

--ion-grid-padding-xs: 5px;
--ion-grid-padding-sm: 5px;
--ion-grid-padding-md: 5px;
--ion-grid-padding-lg: 5px;
--ion-grid-padding-xl: 5px;
```



### 网格宽度



根据屏幕尺寸，来自定义固定的网格宽度值，请为每个断点重写 `--ion-grid-width-{breakpoint}` 的值。



```
--ion-grid-width-xs: 100%;
--ion-grid-width-sm: 540px;
--ion-grid-width-md: 720px;
--ion-grid-width-lg: 960px;
--ion-grid-width-xl: 1140px;
```





























 