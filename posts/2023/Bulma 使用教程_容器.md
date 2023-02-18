Bulma 内置了多种类型的布局组件，可以很方便的使用，本文将介绍这些组件。



## Container



`container` 是一个基础的布局，在该组件中的内容将水平居中，并根据视口断点，进行自适应。该组件一般作为页面的父容器使用，你可以将任意内容包含进去，但最常用的是以下几类：



- `navbar`：导航条

- `hero`：大块的，宏伟的内容，如 Banner

- `section`：独立的块结构

- `footer`：底部区域



`container` 针对各种屏幕类型，配置了屏幕的最大宽度：

```scss
  &.is-fluid
    max-width: none !important
    padding-left: $gap
    padding-right: $gap
    width: 100%
  
  // 大于 $desktop
  +desktop
    max-width: $desktop - $container-offset
  
  // 大于 $desktop，小于 $widescreen
  +until-widescreen
    &.is-widescreen:not(.is-max-desktop)
      max-width: min($widescreen, $container-max-width) - $container-offset
  
  // 大于 $widescreen，小于 $fullhd
  +until-fullhd
    &.is-fullhd:not(.is-max-desktop):not(.is-max-widescreen)
      max-width: min($fullhd, $container-max-width) - $container-offset
  
  // 大于 $widescreen
  +widescreen
    &:not(.is-max-desktop)
      max-width: min($widescreen, $container-max-width) - $container-offset
  
  // 大于 $fullhd
  +fullhd
    &:not(.is-max-desktop):not(.is-max-widescreen)
      max-width: min($fullhd, $container-max-width) - $container-offset

```



`desktop`、`widescreen` 和 `fullhd` 都是在 mixins 中定义的宏，后面跟随的 block，会被作为内容插入。



可以自定义变量：

`$container-offset`：容器边距

`$container-max-width`：容器最大宽度



## Section



页面中一块一块独立的内容，使用 `section` 元素进行定义。它的代码很简单，只是定义了几个变量和 padding。



```scss
$section-padding: 3rem 1.5rem !default
$section-padding-desktop: 3rem 3rem !default
$section-padding-medium: 9rem 4.5rem !default
$section-padding-large: 18rem 6rem !default

.section
  padding: $section-padding
  // Responsiveness
  +desktop
    padding: $section-padding-desktop
    // Sizes
    &.is-medium
      padding: $section-padding-medium
    &.is-large
      padding: $section-padding-large
```



`section` 默认情况下，就是块结构的，默认就是占满一行。`is-medium` 和 `is-large` 只在大于桌面的尺寸视口下有效。



## Level










