---
title: angularr2学习笔记
date: 2017-10-23 11:13:39
categories:
- coding
- font-end
tags: ["Angular2", "SPA"]
toc: true
---

### HTTP

#### 可观察对象 (Observable)

`Http`服务中的每个方法都返回一个 HTTP `Response`对象的`Observable`实例

一个可观察对象是一个事件流，我们可以用数组型操作符来处理它。

### 模板

在标识符前加上`#`就能声明一个模板引用变量。

模板引用变量通常用来引用模板中的某个DOM元素，它还可以引用Angular组件或者Web Component

#### 获取input输入

```ts
@Component({
    selector: 'demo',
    template: `
        <input #box>
        <p>{{box.value}}</p>
    `
})
export class DemoComponent {}
```

#### 获取Form的引用

```html
<form #demoForm="ngForm" (ngSubmit)="onSubmit(demoForm)">
    <input name="name" required>
    <button type="submit" [disabled]="!demoForm.form.valid">Submit</button>
</form>
```

#### 从模板视图中获取元素

ViewChild是属性装饰器，用来从模板视图中获取匹配的元素。视图查询在 ngAfterViewInit 钩子函数调用前完成，因此在 ngAfterViewInit 钩子函数中，才能正确获取查询的元素。

##### @ViewChild 使用模板变量名
```
import { Component, ElementRef, ViewChild, AfterViewInit } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h1>Welcome to Angular World</h1>
    <p #greet>Hello {{ name }}</p>
  `,
})
export class AppComponent {
  name: string = 'Semlinker';

  @ViewChild('greet')
  greetDiv: ElementRef;

  ngAfterViewInit() {
    console.dir(this.greetDiv);
  }
}
```

##### @ViewChild 使用模板变量名及设置查询条件

```
import { Component, TemplateRef, ViewChild, ViewContainerRef, AfterViewInit } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h1>Welcome to Angular World</h1>
    <template #tpl>
      <span>I am span in template</span>
    </template>
  `,
})
export class AppComponent {

  @ViewChild('tpl')
  tplRef: TemplateRef<any>;

  @ViewChild('tpl', { read: ViewContainerRef })
  tplVcRef: ViewContainerRef;

  ngAfterViewInit() {
    console.dir(this.tplVcRef);
    this.tplVcRef.createEmbeddedView(this.tplRef);
  }
}
```

##### @ViewChild 使用类型查询

child.component.ts

```
import { Component, OnInit } from '@angular/core';

@Component({
    selector: 'exe-child',
    template: `
      <p>Child Component</p>  
    `
})
export class ChildComponent {
    name: string = 'child-component';
}
```

app.component.ts

```
import { Component, ViewChild, AfterViewInit } from '@angular/core';
import { ChildComponent } from './child.component';

@Component({
  selector: 'my-app',
  template: `
    <h4>Welcome to Angular World</h4>
    <exe-child></exe-child>
  `,
})
export class AppComponent {

  @ViewChild(ChildComponent)
  childCmp: ChildComponent;

  ngAfterViewInit() {
    console.dir(this.childCmp);
  }
}
```

#### 结构性指令

`<ng-template>`是一个 Angular 元素，用来渲染HTML。 它永远不会直接显示出来。 事实上，在渲染视图之前，Angular 会把`<ng-template>`及其内容替换为一个注释。

等同于js中的模板文件：
```
<script id="myTemplate" type="text/ng-template">
  <div>
    My awesome template!
  </div>
</script>
```
或者：
```
<template id="myTemplate">
  <div>
    My awesome template!
  </div>
</template>
```

要获取上面的模板并实例化它，我们需要使用简单的JavaScript:
```
<div id="host"></div>
<script>
  let template = document.querySelector('#myTemplate');
  let clone = document.importNode(template.content, true);
  let host = document.querySelector('#host');
  host.appendChild(clone);
</script>
```


## 参考

1. [[Angular 2] ElementRef, @ViewChild & Renderer](http://www.cnblogs.com/Answer1215/p/5898545.html)
2. [Angular ngFor, &lt;ng-template&gt; and the compiler](https://toddmotto.com/angular-ngfor-template-element)