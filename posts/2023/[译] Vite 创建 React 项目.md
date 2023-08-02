翻译自：[Vite: Powerful React Project Setup - DEV Community](https://dev.to/equiman/vite-powerful-react-project-g4m)

[Vite](https://vitejs.dev/) 是目前创建React项目的最佳选择。

```
npm create vite@latest <project-name> -- --template <react-swc|react-swc-ts>
# npm 7+, extra double-dash is needed

cd <project-name>
npm install
npm run dev
```

有了这些命令，我们创建了一个非常基本和干净的项目作为起点，但它需要一些额外的工具来自动化任务，这可以让你的生活和开发团队的生活更轻松。

## VSCode

建议在项目设置中而不是在全局设置中进行这些配置。

我们将开始创建一个.vscode文件夹，其中包含一个settings.json文件。

```
# 📄 File: /.vscode/settings.json
-----------------------------------

{
    "explorer.fileNesting.patterns": {
        "*.js": "$(capture).js.map, $(capture).*.js, $(capture)_*.js, $(capture)*.snap",
        "*.jsx": "$(capture).js, $(capture).*.jsx, $(capture)_*.js, $(capture)_*.jsx, $(capture)*.snap",
        "*.ts": "$(capture).js, $(capture).d.ts.map, $(capture).*.ts, $(capture)_*.js, $(capture)_*.ts, $(capture)*.snap",
        "*.tsx": "$(capture).ts, $(capture).*.tsx, $(capture)_*.ts, $(capture)_*.tsx, $(capture)*.snap",
    },
    "emmet.excludeLanguages": [],
    "emmet.includeLanguages": {
        "markdown": "html",
        "javascript": "javascriptreact",
        "typescript": "typescriptreact"
    },
    "emmet.showSuggestionsAsSnippets": true,
    "emmet.triggerExpansionOnTab": true,
    "files.exclude": {
        "**/*.js.map": {
            "when": "$(basename)"
        },
        "**/node_modules": true,
    },
    "html.autoClosingTags": true,
    "javascript.autoClosingTags": true,
    "javascript.suggest.completeFunctionCalls": true,
    "typescript.suggest.completeFunctionCalls": true,
    "javascript.inlayHints.functionLikeReturnTypes.enabled": true,
    "typescript.inlayHints.functionLikeReturnTypes.enabled": true,
    "javascript.inlayHints.parameterNames.enabled": "all",
    "typescript.inlayHints.parameterNames.enabled": "all",
    "javascript.suggest.autoImports": true,
    "search.exclude": {
        "**/coverage": true,
        "**/node_modules": true
    },
    "typescript.autoClosingTags": true,
    "typescript.suggest.autoImports": true,
}
```

有很多VSCode扩展和配置。如果你渴望更多，请查看[VSCode - Essentials](https://dev.to/equiman/my-essential-visual-studio-code-extensions-and-configurations-5197) 和 [VSCode - React Flavored](https://dev.to/equiman/vscode-react-flavored-134h)。

## 调试

从VSCode调试React不需要安装额外的扩展。

```
# 📄 File: /.vscode/launch.json
-----------------------------------

{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome", //or "msedge"
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```



如果要禁用每次打开浏览器，请在项目中的.env文件上添加browser=none。还要更改开发脚本以使用相同的端口。



```
# 📄 File: package.json
-----------------------------------

{
  "scripts": {
-   "dev": "vite",
+   "dev": "vite --port 3000",
  }
}

```



运行npm Run dev命令，然后从“运行和调试”面板启动浏览器。现在，您可以直接在VSCode上添加断点。

或者，如果您只需要快速验证，请在VSCode中使用简单浏览器。



## 扩展



如果您希望在打开项目时显示VSCode扩展建议：



```
# 📄 File: /.vscode/extentions.json
-----------------------------------

{
    "recommendations": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "stylelint.vscode-stylelint",
        "ZixuanChen.vitest-explorer"
    ]
}
```



## Linter



- [ES Lint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 扩展



```
# 📄 File: /.vscode/settings.json
-----------------------------------

{ 
+    "editor.formatOnSave": true,
+    "javascript.format.enable": false,
+    "javascript.validate.enable": true,
+    "typescript.format.enable": false,
+    "typescript.validate.enable": true,
+    "editor.codeActionsOnSave": {
+        "source.fixAll.eslint": true
+    }
}
```



在项目文件夹上进行安装和配置：



```
npm install -D eslint
npm init @eslint/config
```



你可以选择更好的，但我的个人配置是：



```
Use: To check syntax, find problems, and enforce code style
Type of modules: JavaScript modules (import/export)
Framework: React
Typescript: No #or Yes if the project uses it
Run: Browser #and Node if use Next.js
Style guide: Popular -> Standard #JS without semi-colon ;
Format: JSON
```



> ** 分号异常 **
> 
> 
> 因为标准样式指南不使用分号（；），所以请记住这一点。如果不带分号的行后面的下一个语句以以下特殊运算符之一[，（，+，\*，/，-，，.开头，则它将被识别为上一个语句的表达式。然后您需要以；开头；。






