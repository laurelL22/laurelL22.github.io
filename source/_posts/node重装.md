---
title: node重装&切换版本
date: 2019-09-29 15:46:42
tags:
---

### node重装遇到的问题

#### 背景

因业务需求，需要降级node（原先版本是12.4.3），一开始用n进行切换；
* npm install n -g
* n 8.9.3（版本号）
但是切换版本失败（切换不回去了），后采用了nvm。


n会将再装一次node、npm...在/usr/local/bin，即/usr/local/bin/node。导致每次使用nvm use 12.4.0切换后重启终端，node版本都是8.9.3。而且在8.9.3的node下找不到command n，只在12.4.0下的node才能用n命令。这也就是之前说的用n切换node版本失败==


删除8.9.3版本的解决方案：
* 删除已下载的版本
    * n prune                        Remove all downloaded versions except the installed version
* 手动删除/usr/local/bin的以下文件（自己安装的相关文件）
    * rm -rf /usr/local/bin/node
    * rm -rf /usr/local/bin/npm
    * rm -rf /usr/local/bin/npx
    * rm -rf /usr/local/bin/yarn
    * rm -rf /usr/local/bin/haoda-quickapp-cli
* 手动删除/usr/local/bin/node_modules下的所有文件
* ![hh](/images/20190929_node.jpg)


卸载n：
* 执行：npm uninstall -g n
* 手动删除：rm -rf /usr/local/n


#### ！注意
- !!!少用sudo
- !!!node整个管理nvm就够了



### node切换版本、版本回退、版本升级

#### 方案一
* 安装node版本管理模块
    * sudo npm install n -g
* 安装稳定版/最新版
    * sudo n stable
    * sudo n latest
* 版本升级/降级
    * sudo n 版本号       （如：sudo n 8.4.0）
* 检测安装了哪些版本的node
    * n
* 删除版本
    * sudo n rm 版本号


#### 方案二（nvm）
* 检测nvm是否安装
    * command -v nvm
* 列出已安装实例
    * nvm ls
* 安装最新版node
    * nvm install stable
* 安装指定版本的node
    * nvm install [node版本号]
    * 如：
        * 安装4.2.2版本：nvm install 4.2.2
        * 安装4.2系列的最新一个版本：nvm install 4.2
* 切换不同版本node
    * nvm use [node版本号]
    * 如：
        * 切换到4.2.2版本：nvm use 4.2.2
        * 切换到最新的`4.2.x`：nvm use 4.2
        * 切换到iojs：nvm use iojs-v3.2.0
        * 切换到最新版：nvm use node
* 设置默认版本
    * nvm alias default [node版本号]
* 设置别名给不同的版本号：
    * nvm alias awesome-version 4.2.2  -> nvm use awesome-version
    * 取消别名：nvm  unalias awesome-version
* 列出远程服务器上所有的可用版本
    * nvm ls-remote


### 关于nvm

    nvm是将每个node版本的模块都会被安装在各自版本的沙箱里面。
    它的安装目录是在用户文件目录里的，也就是（/Users/(yourname)/.nvm/）

    当在使用某个版本时，安装的程序都运行在各自版本的沙箱里，这使得它能方便针对不同的项目运行不同的node版本，不用再修改系统所使用的node

    但是如果之前已经全局安装过node，那之前安装的全局模块（grunt、gulp）就执行不了。

    因为全局安装的node是存放在/usr/local/bin里的，和nvm运行的目录不一样
