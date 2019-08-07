---
title: webpack-loader
date: 2019-04-12 11:22:41
tags: webpack
---

## loader

(https://webpack.docschina.org/loaders/)

### 用途

webpack可以使用loader来预处理文件，允许打包除JS之外的文件。用于对模块的源代码进行转换。
loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript 或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 import CSS文件！

类型：

- 文件：
  - raw-loader：加载文件原始内容（utf-8）
  - val-loader： 将代码作为模块执行，并将 exports 转为 JS 代码
  - file-loader： 将文件发送到输出文件夹，并返回（相对）URL
  - url-loader 像 file loader 一样工作，但如果文件小于字节限制，可以返回 data URL
  - ref-loader 手动创建所有文件之间的依赖关系
- JSON：
  - json-loader 加载 JSON 文件（默认包含）
  - json5-loader 加载和转译 JSON 5 文件
  - cson-loader 加载和转译 CSON 文件
- 转译(transpiling)
  - script-loader: 在全局上下文中执行一次 JavaScript 文件（如在 script 标签），不需要解析
  - babel-loader 加载 ES2015+ 代码，然后使用 Babel 转译为 ES5
  - buble-loader 使用 Bublé 加载 ES2015+ 代码，并且将代码转译为 ES5
  - traceur-loader 加载 ES2015+ 代码，然后使用 Traceur 转译为 ES5
  - ts-loader 或 awesome-typescript-loader 像 JavaScript 一样加载TypeScript 2.0+
  - coffee-loader 像 JavaScript 一样加载 CoffeeScript
  - fengari-loader 使用 fengari 加载 Lua 代码
- 模板(templating)
  - html-loader：导出html为字符串，需要引用静态资源
  - pug-loader：加载Pug模板并返回一个函数
  - markdown-loader：将markdown转译为HTML
  - react-markdown-loader 使用 markdown-parse parser(解析器) 将 Markdown 编译为 React 组件
  - posthtml-loader 使用 PostHTML 加载并转换 HTML 文件
  - handlebars-loader 将 Handlebars 转移为 HTML
  - markup-inline-loader 将内联的 SVG/MathML 文件转换为 HTML。在应用于图标字体，或将 CSS 动画应用于 SVG 时非常有用。
  - twig-loader 编译 Twig 模板，然后返回一个函数
- 样式：
  - style-loader 将模块的导出作为样式添加到 DOM 中
  - css-loader 解析 CSS 文件后，使用 import 加载，并且返回 CSS 代码
  - less-loader 加载和转译 LESS 文件
  - sass-loader 加载和转译 SASS/SCSS 文件
  - postcss-loader 使用 PostCSS 加载和转译 CSS/SSS 文件
  - stylus-loader 加载和转译 Stylus 文件
- 代码检查和测试(linting && testing)
  - mocha-loader 使用 mocha 测试（浏览器/NodeJS）
  - eslint-loader PreLoader，使用 ESLint 清理代码
  - jshint-loader PreLoader，使用 JSHint 清理代码
  - jscs-loader PreLoader，使用 JSCS 检查代码样式
  - coverjs-loader PreLoader，使用 CoverJS 确定测试覆盖率
- 框架(frameworks)
  - vue-loader 加载和转译 Vue 组件
  - polymer-loader 使用选择预处理器(preprocessor)处理，并且 require() 类似一等模块(first-class)的 Web 组件
  - angular2-template-loader 加载和转译 Angular 组件

### 使用loader（3种方式）

1. 配置：在 webpack.config.js 文件中指定 loader。
   - module.rules 允许你在 webpack 配置中指定多个 loader。
     - rules：数组，指明各个loader。它会作用于匹配到 test 属性所指定规则的每一个文件
     - use：use 指明需要对匹配的文件应用那个loader
   - loader 从右到左地取值(evaluate)/执行(execute)
2. 内联：在每个 import 语句中显式指定 loader。
   - import Styles from ‘style-loader!css-loader?modules!./styles.css’;
   - 使用 ! 将资源中的 loader 分开。每个部分都会相对于当前目录解析。
3. CLI：在 shell 命令中指定它们
   - webpack –module-bind jade-loader –module-bind ‘css=style-loader!css-loader’

### loader特性

- loader 支持链式传递。链中的每个 loader 会将转换应用在已处理过的资源上。一组链式的 loader 将按照相反的顺序执行。链中的第一个 loader 将其结果（也就是应用过转换后的资源）传递给下一个 loader，依此类推。最后，链中的最后一个 loader，返回 webpack 期望 JavaScript。
- loader 可以是同步的，也可以是异步的。
- loader 运行在 Node.js 中，并且能够执行任何 Node.js 能做到的操作。
- loader 可以通过 options 对象配置（仍然支持使用 query 参数来设置选项，但是这种方式已被废弃）。
- 除了常见的通过 package.json 的 main 来将一个 npm 模块导出为 loader，还可以在 module.rules 中使用 loader 字段直接引用一个模块。
- 插件(plugin)可以为 loader 带来更多特性。
- loader 能够产生额外的任意文件。

### babel-loader

允许你使用 Babel 和 webpack 转译 JavaScript 文件。
安装：npm install -D babel-loader @babel/core @babel/preset-env webpack

使用：

```
module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: { // 向 loader 传递 options 选项：
          presets: ['@babel/preset-env']
        }
      }
    }
  ]
}
```



优化：

- 解决babel-loader很慢
  - 确保转译尽可能少的文件
  - 使用exclude排除不需要的源代码
  - 使用 `cacheDirectory` 选项，会将转译的结果缓存到文件系统中。
- Babel 在每个文件都插入了辅助代码，使代码体积过大
  - Babel对一些公共方法使用了非常小的辅助代码(如 _extend)。默认情况下会被添加到每一个需要它的文件中,而babel-runtime供编译模块复用工具函数
  - 可以引入Babel runtime作为一个独立模块，来避免重复引入
  - 禁用Babe自动对每个文件的runtime注入，而是引入 @babel/plugin-transform-runtime 并且使所有辅助代码从这里引用
- transform-runtime & babel-polyfill
  - Babel默认只转换新的JS语法，而不转换新的 API(如：Object.assign)，使用 babel-polyfill，转译新的API
  - Babel 转译后的代码要实现源代码同样的功能需要借助一些帮助函数，可能会重复出现在一些模块里，导致编译后的代码体积变大，webpack提供了单独的包 babel-runtime 供编译模块复用工具函数
    - babel 还为源代码的非实例方法和babel-runtime/helps 下的工具函数自动引用了 polyfill。这样可以避免污染全局命名空间，非常适合于 JavaScript 库和工具包的实现
  - 总结：
    - 具体项目还是需要使用 babel-polyfill，只使用 babel-runtime 的话，实例方法不能正常工作
    - JS库和工具可以使用 babel-runtime，在实际项目中使用这些库和工具，需要该项目本身提供polyfill；

自定义loader：

```
- babel-loader 提供了一个 loader-builder 工具函数，允许用户为 Babel 处理过的每个文件添加自定义处理选项。
```

### css-loader

css-loader解释@import和url()并解析它们
安装：npm install –save-dev css-loader

### sass-loader

加载Sass/Scss文件并且编译成CSS，依赖node-sass
安装:npm install sass-loader node-sass webpack –save-dev

使用:

- 通过将 style-loader 和 css-loader 与 sass-loader 链式调用，可以立刻将样式作用在 DOM 元素

  ```
  module: {
      rules: [{
          test: /\.scss$/,
          use: [
              "style-loader", // 将 JS 字符串生成为 style 节点
              "css-loader", // 将 CSS 转化成 CommonJS 模块
              "sass-loader" // 将 Sass 编译成 CSS，默认使用 Node Sass
          ]
      }]
  }
  ```

- 生产环境下通常使用mini-css-extract-plugin将样式表抽里程专门的单独文件。

- 提取样式表：extract-loader；mini-css-extract-plugin

### file-loader

file-loader将文件上的import/require()解析为url，并将文件发送到输出目录中。

- 默认情况下，生成文件的文件名，是文件内容的 MD5 哈希值，并会保留所引用资源的原始扩展名
- 选项：
  - name：为目标文件指定自定义文件名模板
  - outputPath： 指定放置目标文件的文件系统路径
  - publicPath：指定目标文件的自定义公共路径
  - context：指定自定义文件上下文