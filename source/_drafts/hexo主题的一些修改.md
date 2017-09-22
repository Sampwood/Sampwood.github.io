---
title: hexo主题的一些修改
date: 2017-08-23 20:07:31
categories:
- 随笔
tags:
- hexo
---
多级分类标签的href有错误，多级分类的href会写到根分类上面

修改方法:
```
yilia/layout/_partial/post/category.ejs
第七行的a标签改为：
<a href="<%= config.root %><%= tag.path %>" class="article-tag-list-link color<%= tag.name.length % 5 + 1 %>"><%-tag.name%></a>
```

### hexo在模板可以使用的数据

#### post.categories

其数据结构如下：
```
[
    {
       name: 'coding',
       _id: 'cj6owqyde000eawi9iokysyfe',
       slug: [Getter],
       path: [Getter],
       permalink: [Getter],
       posts: [Getter],
       length: [Getter] 
    },
    ...
]

path: categories/coding/
permalink: https://sampwood.github.io/categories/coding/
```