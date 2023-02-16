Bulma 使用教程 - 导航条

Bulma 的导航条，位于顶部，响应式且功能丰富。

## 结构

![](C:\Users\shiweifu\AppData\Roaming\marktext\images\2023-02-16-11-49-51-image.png)

- `navbar`：主导航条容器
  
  - `navbar-brand`：一直可见，位于左侧，放需要一直可见的内容，如 logo、链接等。
    
    - `navbar-burger`：汉堡图标。在移动端设备上会显示。通过 JS 代码控制是菜单否展开。
  
  - `navbar-menu`：右侧菜单。在桌面状态下，除去 `navbar-brand` 以外的右侧全部区域，都在这里。移动端状态下，默认隐藏，需要通过代码设置展开。
    
    - `navbar-start`：菜单的左侧区域。
    
    - `navbar-end`：菜单的右侧区域。
      
      - `navbar-item`：独立的菜单项，可以是 `a` 或者是 `div`。
        
        - `navbar-link`：子菜单项。
        
        - `navbar-dropdown`：下拉菜单。
          
          - `navbar-divider`：菜单分割线。

## 实现

### 一个独立的子菜单

菜单组件不一定在顶部，也可以在页面中，其他区域使用：

```html
<nav class="navbar" role="navigation" aria-label="dropdown navigation">
  <div class="navbar-item has-dropdown">
    <a class="navbar-link">
      Docs
    </a>

    <div class="navbar-dropdown">
      <a class="navbar-item">
        Overview
      </a>
      <a class="navbar-item">
        Elements
      </a>
      <a class="navbar-item">
        Components
      </a>
      <hr class="navbar-divider">
      <div class="navbar-item">
        Version 0.9.4
      </div>
    </div>
  </div>
</nav>
```

![](C:\Users\shiweifu\AppData\Roaming\marktext\images\2023-02-16-12-12-50-image.png)



`navbar` 是使用 `flex` 进行实现的，源码主要是定义在 `navbar.sass` 文件中。



常用的定制属性都以 scss 变量的形式，进行定义。只需要覆盖这些变量，即可完成自定义。



```scss

```








