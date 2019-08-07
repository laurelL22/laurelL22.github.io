---
title: webpack中按需打包的babel插件实现
date: 2019-04-15 11:17:37
tags: webpack
---

## babel插件实现按需打包的功能

### 目的


```
import {flatten,join} from 'lodash';

转化成=========>

import flatten from 'lodash/flatten';
import join from 'lodash/join';
```

### 实现

- 配置.babelrc

  ```json
  {
      "plugins": [
          [
              "babel-plugin-sui-import", // 这个是自己写的插件名
              {
                  "libraryName": "vant", // 在引用哪个库时候使用自己写的这个插件
                  "libraryDirectory": "lib"
              }
          ]
      ]
  }
  ```

- 引入脚本，进行对比

  ```js
  // import {flatten,join} from 'lodash' // 打包后大小：483k
  
  
  import flatten from 'lodash/flatten' // 打包后大小：21.3K
  import join from 'lodash/join'
  ```

  两种引入方式的抽象语法树的差别，只是IMportDeclaration不同

- 整理插件代码到node_modules文件中

  babel插件的文件名，必须以babel-plugin-xxx命名，否则引入不成功；babel插件返回的是一个对象，里面是个访问者模式的visitor对象，里面是转化代码

  ```js
  const t = require('@babel/types');
  const humps = require('humps');
  
  module.exports = () => {
      return {
          visitor: {
              ImportDeclaration(
                  path,
                  {
                  opts: { libraryName, libraryDirectory }
                  }
              ) {
                  if (!t.isStringLiteral(path.node.source, { value: libraryName })) {
                      return;
                  }
                  const specifiers = path.node.specifiers;
                  const declarations = specifiers.map(specifier => {
                      return t.ImportDeclaration(
                          [t.importDefaultSpecifier(specifier.local)],
                          t.StringLiteral(
                          `${libraryName}/${libraryDirectory}/${humps.camelize(
                              specifier.local.name
                          )}`
                          )
                      );
                  });
                  path.replaceWithMultiple(declarations);
              }
          }
      };
  };
  ```