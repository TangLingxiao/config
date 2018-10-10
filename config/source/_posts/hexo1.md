---
title: Hexo的Next主题常用设置
date: 2018-10-10 23:20:37
tags: Next
categories: Hexo
---

   #### 添加「标签」页面

在终端下，定位到Hexo站点目录下。使用 hexo new page 新建一个页面，命名为tags：

```
hexo new page tags
```

会在 source 目录下生成 tags 目录，修改 tags 目录下 index.md 文件为：

```
title: 我是一个标签
date: YYYY-MM-DD HH:mm:ss
comments: false
type: "tags"
```

修改 next 主题配置 _config.yml 为：

```
menu:
  tags: /tags/ || tags
```



#### 添加「分类」页面

在终端下，定位到Hexo站点目录下。使用 hexo new page 新建一个页面，命名为categories：

```
hexo new page categories
```

会在 source 目录下生成 categories 目录，修改 categories 目录下 index.md 文件为：

```
title: 我是一个分类
date: YYYY-MM-DD HH:mm:ss
comments: false
type: "categories"
```

修改 next 主题配置 _config.yml 为：

```
menu:
  categories: /categories/ || th
```



#### 解决 Next v5 版本 Valine 评论系统报错

按照这个 [PR](https://github.com/theme-next/hexo-theme-next/pull/345/files) 修改涉及到的相关文件即可。



