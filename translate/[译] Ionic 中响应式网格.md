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

