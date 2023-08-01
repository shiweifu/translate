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


