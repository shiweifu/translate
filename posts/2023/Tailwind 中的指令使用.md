Tailwind 的常用指令



**@variants：用于为样式指定可应用的变体，例如 hover、focus、active 等。**



```
.btn {
  @apply bg-blue-500 text-white p-2 rounded-md;
  @apply hover:bg-blue-600;
  @apply focus:outline-none focus:ring-2 focus:ring-blue-500;
}
```



**@screen：用于根据屏幕尺寸定义样式。**



```
@media (min-width: 640px) {
  .container {
    @apply mx-auto px-4 max-w-3xl;
  }
}

@media (min-width: 768px) {
  .container {
    @apply max-w-4xl;
  }
}

@media (min-width: 1024px) {
  .container {
    @apply max-w-5xl;
  }
}
```





@layer：用于将样式添加到指定的图层中，例如 utilities、components 等。



```
@layer components {
  .alert {
    @apply border-l-4 p-4;
    border-color: var(--alert-color);
  }
}
```



**@responsive：用于定义响应式样式。**



```
.btn {
  @apply bg-blue-500 text-white p-2 rounded-md;
  @apply hover:bg-blue-600;
  @apply focus:outline-none focus:ring-2 focus:ring-blue-500;

  @responsive {
    font-size: 1.2rem;
  }
}
```



**@variants 和 @screen 可以结合使用，用于根据屏幕尺寸和变体定义样式。**



```
.btn {
  @apply bg-blue-500 text-white p-2 rounded-md;

  @variants hover, focus {
    @apply bg-blue-600;
  }

  @screen md {
    @apply px-4;
  }
}
```





**@tailwind：用于在样式中引用 Tailwind 的配置变量，例如颜色、边距等。**



```
.btn {
  background-color: var(--tw-bg-blue-500);
  color: var(--tw-text-white);
  padding: var(--tw-p-2);
  border-radius: var(--tw-rounded-md);

  &:hover {
    background-color: var(--tw-bg-blue-600);
  }

  &:focus {
    outline: none;
    box-shadow: var(--tw-ring-2) var(--tw-ring-blue-500);
  }
}
```





**@variants 和 @responsive 可以结合使用，用于根据屏幕尺寸和变体定义响应式样式。**



```
.btn {
  @apply bg-blue-500 text-white p-2 rounded-md;

  @variants hover, focus {
    @apply bg-blue-600;
  }

  @responsive {
    font-size: 1.2rem;
    @variants hover, focus {
      font-size: 1.4rem;
    }
  }
}
```


