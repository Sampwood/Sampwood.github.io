# sampwood.github.io
基于hexo搭建的博客，主题使用的是[hexo-theme-yilia](https://github.com/litten/hexo-theme-yilia)。

希望能记录下一点东西。。。


## usage
- 发布新的博客
```
hexo new "new post"
hexo g -d
```

- draft文档
```
hexo new draft "new draft"
hexo publish "new draft"
```

- 更新yilia代码
```
git remote add yilia https://github.com/Sampwood/hexo-theme-yilia.git
git subtree pull --prefix=themes/yilia yilia master
```

- 提交yilia代码
```
git subtree push --prefix=themes/yilia yilia master
```