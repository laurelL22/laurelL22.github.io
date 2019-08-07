---
title: adblock-plus-blocking-filter
date: 2018-03-26 14:59:43
tags:
---
### 网页内容被adblock plus插件拦截

[adblock plus chinalist easylist](https://easylist-downloads.adblockplus.org/easylistchina+easylist.txt)

- 常规广告拦截过滤规则：
  - 包含常见的广告内容js及目录
- 常规网页元素过滤规则:
  - 包含常见的广告class、id元素的命名
- 联盟广告过滤规则 Ads-Union:
  - 包含常见的联盟广告调用JS和域名，这个过滤规则最恨，几乎涵盖了所有的联盟广告平台
- 弹窗 Popups
  - 包含常见的弹窗广告
- 特定广告过滤规则 Specific advert blocking filters
- 特定元素过滤规则 Specific element hiding rules
  - 这个规则将一些网站固定的某个广告位加以过滤
- CSS样式白名单 CSS Whitelist,包含了一些知名网站的忽略规则

### 遇见的坑：

ff下有类名为icon-advertise的样式被屏蔽掉