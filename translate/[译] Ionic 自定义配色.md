翻译自：[Ionic 6 Custom Color Variables - DEV Community](https://dev.to/readymadecode/ionic-6-custom-color-variables-j5n)



为你的 App 创建自定义 UI。在 Ionic 6 中，更改主体或者自定义颜色，最快速的方式是设置主色、次级颜色、深色和浅色。下面我们将修改 Ionic 中预定义的颜色变量。



#### 在 Ionic 6 中，添加自定义颜色



首先访问 [ionic color generator](https://ionicframework.com/docs/theming/color-generator)，根据你的情况，创建和修改你的颜色，并复制 `:root` 对象，到你的 `src/themes/variables.scss` 文件中。



```
:root {
    --ion-color-primary: #3880ff;
    --ion-color-primary-rgb: 56,128,255;
    --ion-color-primary-contrast: #ffffff;
    --ion-color-primary-contrast-rgb: 255,255,255;
    --ion-color-primary-shade: #3171e0;
    --ion-color-primary-tint: #4c8dff;

    --ion-color-secondary: #5260ff;
    --ion-color-secondary-rgb: 82,96,255;
    --ion-color-secondary-contrast: #ffffff;
    --ion-color-secondary-contrast-rgb: 255,255,255;
    --ion-color-secondary-shade: #4854e0;
    --ion-color-secondary-tint: #6370ff;

    --ion-color-tertiary: #5260ff;
    --ion-color-tertiary-rgb: 82,96,255;
    --ion-color-tertiary-contrast: #ffffff;
    --ion-color-tertiary-contrast-rgb: 255,255,255;
    --ion-color-tertiary-shade: #4854e0;
    --ion-color-tertiary-tint: #6370ff;

    --ion-color-success: #2dd36f;
    --ion-color-success-rgb: 45,211,111;
    --ion-color-success-contrast: #000000;
    --ion-color-success-contrast-rgb: 0,0,0;
    --ion-color-success-shade: #28ba62;
    --ion-color-success-tint: #42d77d;

    --ion-color-warning: #ffc409;
    --ion-color-warning-rgb: 255,196,9;
    --ion-color-warning-contrast: #000000;
    --ion-color-warning-contrast-rgb: 0,0,0;
    --ion-color-warning-shade: #e0ac08;
    --ion-color-warning-tint: #ffca22;

    --ion-color-danger: #eb445a;
    --ion-color-danger-rgb: 235,68,90;
    --ion-color-danger-contrast: #ffffff;
    --ion-color-danger-contrast-rgb: 255,255,255;
    --ion-color-danger-shade: #cf3c4f;
    --ion-color-danger-tint: #ed576b;

    --ion-color-medium: #8c97c5;
    --ion-color-medium-rgb: 140,151,197;
    --ion-color-medium-contrast: #000000;
    --ion-color-medium-contrast-rgb: 0,0,0;
    --ion-color-medium-shade: #7b85ad;
    --ion-color-medium-tint: #98a1cb;

    --ion-color-light: #f4f5f8;
    --ion-color-light-rgb: 244,245,248;
    --ion-color-light-contrast: #000000;
    --ion-color-light-contrast-rgb: 0,0,0;
    --ion-color-light-shade: #d7d8da;
    --ion-color-light-tint: #f5f6f9;

}
```



如果你想创建不同风格的的颜色，如 Instrgram 配色，直接声明变量即可。下面是一些变量的定义：



```
:root {
    --ion-color-instagram: #fd4c76;
    --ion-color-instagram-rgb: 253, 76, 118;
    --ion-color-instagram-contrast: #ffffff;
    --ion-color-instagram-contrast-rgb: 255, 255, 255;
    --ion-color-instagram-shade: #fa3462;
    --ion-color-instagram-tint: #d61845;
}

.ion-color-instagram {
  --ion-color-base: var(--ion-color-instagram) !important;
  --ion-color-base-rgb: var(--ion-color-instagram-rgb) !important;
  --ion-color-contrast: var(--ion-color-instagram-contrast) !important;
  --ion-color-contrast-rgb: var(--ion-color-instagram-contrast-rgb) !important;
  --ion-color-shade: var(--ion-color-instagram-shade) !important;
  --ion-color-tint: var(--ion-color-instagram-tint) !important;
}
```



现在，我们可以在我们的 App 中，使用刚刚定义的 Ionic 颜色变量了。举个例子：



```
<ion-icon name="logo-twitter" color="secondary"></ion-icon>
<ion-item color="primary"></ion-item>
```



下面是使用我们我们在上面刚刚定义的颜色。



```
<ion-icon name="logo-instagram" color="instagram"></ion-icon>
```



---



请分享这篇文章，并给我一些反馈，这是我继续写下去的动力。



更多内容，请查看我的网站：[tutorials visit my website](https://www.readymadecode.com/tutorials/).



感谢~

Happy Coding :)