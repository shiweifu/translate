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



您将被要求安装额外的软件包。回答是。完成更新配置规则时：



```
# 📄 File: .eslintrc.json
-----------------------------------

{
    "rules": {
+     "no-console": "warn",+     "react/prop-types": "off",+     "react/self-closing-comp": "warn",+     "react/react-in-jsx-scope": "off"
    },
+   "settings": {+     "react": {+       "version": "detect"+     }+   }
}
```



如果你使用的是 `TypeScript`，你同样需要配置：



```
# 📄 File: .eslintrc.json
-----------------------------------

{
    "parserOptions": {
+     "project": ["tsconfig.json"],+     "createDefaultProgram": true
    },
    "rules": {
      "no-console": "warn",
      "react/prop-types": "off",
      "react/self-closing-comp": "warn",
+     "@typescript-eslint/consistent-type-definitions": ["error", "type"],+     "@typescript-eslint/explicit-function-return-type": "off",
  },
}
```



创建一个 `.eslintignore` 文件，在当前项目的根目录：



```
# 📄 File: .eslintignore
-----------------------------------

build
coverage
dist
```



> 不需要添加node_modules，因为默认情况下会忽略它。



如果您不想使用ES Lint扩展，请在脚本末尾添加list和fix命令：



```
# 📄 File: package.json
-----------------------------------

{
  "scripts": {
+    "lint": "eslint .",+    "lint:fix": "eslint . --fix --ext .js,.jsx,.ts,.tsx"
  }
}

```



## 忽略 React 引入错误



> 自从React 17以来，您不再需要导入React来使用JSX。但我们仍然需要进口React来使用Hooks或React提供的其他出口。



为了避免ES Lint对导入React发出警告，请添加一个插件：



```
# 📄 File: .eslintrc.json
-----------------------------------

{
    "extends": [
      "plugin:react/recommended",
      "standard-with-typescript",
+     "plugin:react/jsx-runtime",    
    ],
}
```



## 空行



如果要保留空行以前的定义：



```
# 📄 File: .eslintrc.json
-----------------------------------

{
    "rules": {
+       "padding-line-between-statements": [
+           "error",
+           {
+               "blankLine": "always",
+               "prev": "*",
+               "next": "return"
+           },
+           {
+               "blankLine": "always",
+               "prev": [
+                   "const",
+                   "let",
+                   "var"
+               ],
+               "next": "*"
+           },
+           {
+               "blankLine": "any",
+               "prev": [
+                   "const",
+                   "let",
+                   "var"
+               ],
+               "next": [
+                   "const",
+                   "let",
+                   "var"
+               ]
+           }
+       ]
    },
}
```



## 自动排序



如果您不想处理排序导入和属性，请设置此配置。



```
# 📄 File: .eslintrc.json
-----------------------------------

{
    "rules": {
+       "import/order": [
+           "warn",
+           {
+               "pathGroups": [
+                   {
+                       "pattern": "~/**",
+                       "group": "external",
+                       "position": "after"
+                   }
+               ],
+               "newlines-between": "always-and-inside-groups"
+           }
+       ],
+       "react/jsx-sort-props": [
+           "warn",
+           {
+               "callbacksLast": true,
+               "shorthandFirst": true,
+               "noSortAlphabetically": false,
+               "reservedFirst": true
+           }
+       ]
    },
}
```



## 格式化



> ES Lint就足够了，Prettier是可选的，因为它比ES Lint具有更好的性能格式化。如果你想使用它，那就去吧。但有一个问题，他们都在努力尝试格式化代码，所以你必须知道如何配置它们才能协同工作。



如果你想使用它，那就去吧。



- Prettier-代码格式化程序扩展



```
# 📄 File: /.vscode/settings.json
-----------------------------------

{
-    "editor.codeActionsOnSave": {
-        "source.fixAll.eslint": true
-    }
+    "eslint.probe": [
+        "javascript",
+        "javascriptreact",
+        "typescript",
+        "typescriptreact"
+    ],
+    "[javascript][typescript]": {
+        "editor.defaultFormatter": "esbenp.prettier-vscode",
+        "editor.formatOnSave": false,
         // Runs Prettier, then ESLint
+        "editor.codeActionsOnSave": [
+            "source.formatDocument",
+            "source.fixAll.eslint"
+        ],
+    }
}
```



安装 Prettier：



```
npm install -D prettier 
```



ESLint 的 prettier 插件：



```
npm install -D eslint-config-prettier
```



或者 TSLint（TS）针对 prettier 的插件：



```
npm install -D tslint-config-prettier
```



创建一个 `.prettierignore` 文件，在项目的根目录：



```
# 📄 File: .prettierignore
-----------------------------------

build
coverage
dist
package-lock.json
```



无需添加node_modules，因为默认情况下它被忽略了。



如果您不想使用ES Lint扩展，请在脚本末尾添加list和fix命令：



```
# 📄 File: package.json
-----------------------------------

{
  "scripts": {
+    "lint": "eslint .",
+    "lint:fix": "eslint . --fix --ext .js,.jsx,.ts,.tsx"
  }
}
```



## HTML Linter



- [HTMLHint](https://marketplace.visualstudio.com/items?itemName=HTMLHint.vscode-htmlhint)



```
npm install -D htmlhint
```



如果你还想用eslint来lint HTML，请安装这个额外的插件：



```
# 📄 File: .eslintrc.json
-----------------------------------

{
    "plugins": [
        "react",
+       "html"
    ],
}
```



## CSS Linter



- [Stylelint](https://marketplace.visualstudio.com/items?itemName=stylelint.vscode-stylelint) 扩展



在项目文件夹上进行安装和配置：



```
npm install -D stylelint stylelint-config-standard stylelint-config-idiomatic-order
```



在存储库的顶层创建一个名为.stylelintrc.json的配置文件。



```
# 📄 File: .stylelintrc.json
-----------------------------------

{
  "extends": [
    "stylelint-config-standard",
    "stylelint-config-idiomatic-order"
  ],
  "rules": {
    "declaration-colon-newline-after": "always-multi-line"
  },
  "ignoreFiles": [
    "build/**",
    "coverage/**",
    "dist/**",
    "**/*.js",
    "**/*.jsx",     "**/*.ts",
    "**/*.tsx"   ] 
}
```



如果您要使用样式化组件，请同时安装：



```
npm install -D stylelint-config-styled-components
```

```
# 📄 File: .stylelintrc.json
-----------------------------------

{
  "extends": [
    "stylelint-config-standard",
    "stylelint-config-idiomatic-order",
+   "stylelint-config-styled-components"
  ],
  "ignoreFiles": [
    "build/**",
    "coverage/**",
    "dist/**",
    "**/*.js",
-   "**/*.jsx", 
    "**/*.ts",
-   "**/*.tsx" 
  ] 
}
```



为了防止VS Code的内置linters和Stylelint报告相同的错误，可以禁用内置linters。



```
# 📄 File: /.vscode/settings.json
-----------------------------------

{ 
+    "stylelint.enable": true,
+    "css.validate": false,
+    "less.validate": false,
+    "scss.validate": false,
+    "[css][scss]": {
+        "editor.defaultFormatter": "stylelint.vscode-stylelint",
+        "editor.codeActionsOnSave": [
+            "source.fixAll.stylelint"
+        ],
+    }
}
```



Styleint有170多条规则。有时，它会向您显示一个错误，该错误实际上会导致问题。



## Git Linter



如果项目没有git存储库。首先，运行：



```
git init
```



- [Lint Staged](https://github.com/okonet/lint-staged)



它对Husky有效，并且只对暂存的git文件运行linters。通过这样做，您可以确保没有错误进入存储库并强制执行代码样式。



在项目文件夹上进行安装和配置：



```
npx mrm@3 lint-staged
```



## 测试



使用Vitest。它与Jest语言API兼容，因此您不需要重新学习语法。



```
npm install -D vitest
```



有了全局标志，您就不需要在每个文件上导入去依赖项，从而自动支持Jest。



```
# 📄 File: vitest.config.ts
-----------------------------------

- import { defineConfig } from 'vite'
+ import { defineConfig } from 'vitest/config'

export default defineConfig({
+  test: {
+    globals: true,
+  },
})
```



如果您使用的是typescript，也可以添加此配置。



```
# 📄 File: tsconfig.json
-----------------------------------

{ 
+  "compilerOptions": {
+    "types": ["vitest/globals"],
+  }
}
```



下一步，它不是必需的。但是，如果您想利用IntelliSense，建议使用以下内容启动测试文件：



```
# 📄 Files: *.test.js
-----------------------------------

import { it, expect, describe } from "vitest";
```



更新运行测试脚本如下：



```
# 📄 File: package.json
-----------------------------------

{
  "scripts": {
+    "test":    "vitest --run --reporter verbose",+    "test:w":  "vitest",+    "test:ui": "vitest --ui",
  }
}
```



## 快照



如果希望快照与测试文件位于同一级别，而不是__snapshots__文件夹。添加此属性。



```
# 📄 File: vitest.config.ts
-----------------------------------

export default defineConfig({
   test: {
     globals: true,
+    resolveSnapshotPath: (testPath, snapExtension) => testPath + snapExtension,
   },
})
```



## 覆盖率



对于覆盖率报告，我们需要安装@vitest/coverage-v8



```
npm install -D @vitest/coverage-v8
```



更新运行测试脚本如下：



```
# 📄 File: package.json
-----------------------------------

{
  "scripts": {
+    "test:c":  "vitest run --coverage",
+    "test:cw": "vitest watch --coverage"
  }
}
```



添加@vitest/coverage-v8配置。

```
# 📄 File: vitest.config.ts
-----------------------------------

import { defineConfig } from 'vitest/config'

export default defineConfig({
   test: {
     globals: true,
+    reporters: ['verbose'],
+    coverage: {
+     all: true,
+      reporter: ['text', 'html', 'lcov'],
+      include: ['**/src/**/*.{js,jsx,ts,tsx}'],
+      exclude: [
+        '**/src/main.{js,jsx,ts,tsx}',
+        '**/*.types.{ts,tsx}',
+        '**/*.test.{js,jsx,ts,tsx}',
+        '**/src/vite-env*',
+      ],
+      statements: 0,
+      branches: 0,
+      functions: 0,
+      lines: 0,
+    },
  },
})
```



## RTL（React测试库）



运行该命令，安装 RTL。



```
npm install -D @testing-library/react @testing-library/dom @testing-library/user-event @testing-library/jest-dom
```



同样需要安装 `jsdom` （或者 `happy-dom`）



```
npm install -D jsdom
```



添加 `environment`



```
# 📄 File: vitest.config.ts
-----------------------------------

import { defineConfig } from 'vitest/config'

export default defineConfig({
   test: {
     globals: true,
+    environment: 'jsdom',
   },
})
```



使用 `setupFiles` 属性，我们可以扩展 `jest-dom` 匹配器，而无需在每个测试文件中导入它们。



```
# 📄 File: .vitest/setup.ts
-----------------------------------

+ /* Extend Matchers */
+ import '@testing-library/jest-dom'
```



```
# 📄 File: vitest.config.ts
-----------------------------------

import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    globals: true,
+   setupFiles: '.vitest/setup.ts',
  },
})
```



如果您使用esint，最好使用以下命令添加RTL linters：



```
npm install --D eslint-plugin-testing-library eslint-plugin-jest-dom
```



并将这些配置添加到 `esint`` 配置文件中。



```
# 📄 File: .eslintrc.json
-----------------------------------

{
    "extends": [
        "plugin:react/recommended",
        "standard-with-typescript",
        "plugin:react/jsx-runtime",
        "prettier",
+       "plugin:testing-library/react",+       "plugin:jest-dom/recommended"
    ],
    "plugins": [
        "react",
        "html",
+       "testing-library",+       "jest-dom"
    ],
    "rules": {
+       "testing-library/await-async-query": "error",+       "testing-library/no-await-sync-query": "error",+       "testing-library/no-debugging-utils": "warn",+       "testing-library/no-dom-import": "off",+       "jest-dom/prefer-checked": "error",+       "jest-dom/prefer-enabled-disabled": "error",+       "jest-dom/prefer-required": "error",+       "jest-dom/prefer-to-have-attribute": "error"
    },
}
```



在setupFiles中添加一个配置，以避免全局matcher和jest-dom冲突。
































