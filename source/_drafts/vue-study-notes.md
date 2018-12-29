---
title: vue学习笔记
tags:
  - vue
---

### props

props不能直接修改值，但是可以修改它的属性，并且外面的对象也会改变，它们指向相同的对象：

parent.vue:
```
<child-vue :config="conf"></child-vue>

conf = {
	name: 'conf'
}

console.log(this.conf.name) //当child.vue中改变props.config的值后，this.conf也会变
```

child.vue:
```
props: ['config']

this.config.name = 'new value'

```

**最好是用sync和update:prop 的方式来进行双向数据绑定**