---
title: webpack多入口文件页面配置
date: 2019-04-15 11:16:00
tags: webpack
categories: 
	- interview
---

## webpack多入口文件页面配置

webpack的多页面文件的打包配置

```
单页应用程序和多页应用程序的webpack配置文件绝大部分是相同的，loader、output、plugins这些基本不需改动；

需要改动的一般都是入口文件entry，如果用到抽离css样式的插件extract-text-webpack-plugin、自动模板插件html-webpack-plugin,需要进行额外的改写。
```

- entry

  ```js
  // 多页应用程序，则需要多个入口文件(如：项目里有多个入口文件)
  entry: {
      home: resolve(__dirname, 'src/home/index.js'),
      about: resolve(__dirname, 'src/about/index.js')
  }
  ```

- extract-text-webpack-plugin

  主要是为了抽离css样式，防止将样式打包在js中引起页面样式加载错乱或者js脚本体积过大等情况；

  1. 多页程序，因为存在多个入口文件以及对应的多个页面，每个页面都有自己的css样式，所以需要为每个页面各自配置:

     ```js
     //为每个页面定义一个 ExtractTextPlugin
     const homeExtractCss = new ExtractTextPlugin('home/[name].[contenthash].css')
     const aboutExtractCss = new ExtractTextPlugin('about/[name].[contenthash].css')
     
     // 多页应用程序，则需要多个入口文件(如：项目里有多个入口文件)
     plugins: [
         homeExtractCss
         aboutExtractCss
     ]
     ```

  2. 每个页面只需要知己的css样式，不需要别的页面css样式，以防样式覆盖和代码冗余，需要对loader配置进行修改：

     ```js
     module: {
         rules: [ // 每个页面的 ExtractTextPlugin 只处理这个页面的样式文件
             {
                 test: /src(\\|\/)home(\\|\/)css(\\|\/).*\.(css|scss)$/,
                 use: homeExtractCss.extract({
                     fallback: 'style-loader',
                     use: ['css-loader', 'postcss-loader', 'sass-loader']
                 })
             },  {
                 test: /src(\\|\/)about(\\|\/)css(\\|\/).*\.(css|scss)$/,
                 use: aboutExtractCss.extract({
                     fallback: 'style-loader',
                     use: ['css-loader', 'postcss-loader', 'sass-loader']
                 })
             }
         ]
     }
     // 每个页面都有各自的 ExtractTextPlugin，所以需要都声明一遍
     plugins: [homeExtractCss, aboutExtractCss]
     ```

- html-webpack-plugin
  在单页应用程序和多页应用程序中的 webpack配置没什么区别，有几个页面，就对每个页面进行配置即可

  ```js
  new HtmlWebpackPlugin({
      filename: 'about/about.html', // 'home/home.html'
      template: 'src/about/html/index.html', // 'src/about/html/index.html'
      inject: true,
      minify: {
          removeComments: true,
          collapseWhitespace: true
      }
  })
  ```

- 自动配置
  每个页面都是 src/目录下的一个文件夹，这个文件夹中有两个子目录，分别存放这个页面的模板 html，样式文件 css，还有一个入口文件 index.js

  通过一个通用方法来获取所有的页面名称（每个页面都是 ./src下的一个目录，目录名即为页面名）：

  ```js
  function getEntry () { // 借助glob库，遍历.src/目录下具有这种规律src/**/html/*.html的子目录，通过正则匹配出子目录的名称
    let globPath = 'src/**/html/*.html'
    // (\/|\\\\) 这种写法是为了兼容 windows和 mac系统目录路径的不同写法    
    let pathDir = 'src(\/|\\\\)(.*?)(\/|\\\\)html'    
    let files = glob.sync(globPath)    
    let dirname, entries = []    
    for (let i = 0; i < files.length; i++) {        
      dirname = path.dirname(files[i])        
      entries.push(dirname.replace(new RegExp('^' + pathDir), '$2'))    
    }
    return entries
  }
  ```

  