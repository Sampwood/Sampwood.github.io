---
title: angular4学习笔记
date: 2017-10-23 11:13:39
categories:
- coding
- TS
tags: 
- Angular4
- SPA
toc: true
---

### HTTP

#### 可观察对象 (Observable)

`Http`服务中的每个方法都返回一个 HTTP `Response`对象的`Observable`实例

一个可观察对象是一个事件流，我们可以用数组型操作符来处理它。
<!--more-->
### 模板

**TODO**: ngModel

在标识符前加上`#`就能声明一个模板引用变量。

模板引用变量通常用来引用模板中的某个DOM元素，它还可以引用Angular组件或者Web Component

#### 获取input输入

```typescript
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
```typescript
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

```typescript
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

### 使用trackBy，作为items的key

**TODO**: ngFor index count odd...

Angular把对象或keys与特定的DOM节点相互联系起来。如此当对象改变或UI需要重新渲染时，框架能更有效的处理。Angular的`ngFor`默认使用对象的引用(?)做检查。但我们可以自定义跟踪方案：

```html
<ul>
  <li *ngFor="let contact of contacts | async; trackBy: trackById;">
    <contact-card [contact]="contact"></contact-card>
  </li>
</ul>
```

```javascript
trackById(index, contact) {
  return contact.id;
}
```

`trackBy`通知angular使用每个contact的id来建立联系。

Notes: 如果使用数组的index来作为id，那重新排序之后，就会导致DOM对象的销毁和重建而不是复用

### 依赖注入

使用依赖注入就不需要在class内部去新建实例对象，这样可以更好的支持功能扩展，例如构造方法的改变（参数的增加）

### 属性装饰器

#### 宿主元素（Host Element）

为了将Angular组件渲染成DOM树，需要将Angular组件与一个DOM元素相关联，这样的DOM元素称之为：**宿主元素**。
宿主元素的概念适用于指令（应用指令的元素）和组件（组件本身）。

组件可以与宿主元素进行如下方式的交互：
- 监听宿主元素事件 ---> `@HostListener`
- 更改宿主元素属性 ---> 指令如`ngClass`，[attr.style]以及`@HostBinding`
- 调用宿主元素方法

#### @HostListener

HostListener是属性装饰器，用来为宿主元素添加事件监听

##### HostListener 装饰器定义
```typescript
/**
 * Type of the HostListener decorator / constructor function.
 *
 * @stable
 */
export interface HostListenerDecorator {
  (eventName: string, args?: string[]): any;
  new (eventName: string, args?: string[]): any;
}

/**
 * Type of the HostListener metadata.
 *
 * @stable
 */
export interface HostListener {
  eventName?: string;
  args?: string[];
}
```

##### HostListener 装饰器应用
demo.directive.ts
```typescript
import { Directive, HostListener } from '@angular/core';

@Directive({selector: 'button[counting]'})
class CountClicks {
  numberOfClicks = 0;

  @HostListener('click', ['$event.target'])
  onClick(btn) {
    console.log('button', btn, 'number of clicks:', this.numberOfClicks++);
  }
}
```

demo.component.ts
```typescript
import { Component} from '@angular/core';

@Component({
  selector: 'app',
  template: '<button counting>Increment</button>',
})
class App {}
```

此外，我们也可以监听宿主元素外，其它对象产生的事件，如`window`或`document`对象。例如：
```typescript
...
@HostListener('document:click', ['$event'])
onClick(btn: Event) {
    if (this.el.nativeElement.contains(event.target)) {
        this.highlight('yellow');
    } else {
        this.highlight(null);
    }
}
...
```

#### Host Event Listener

也可以在指令的 metadata 信息中，设定宿主元素的事件监听信息，具体示例如下：

demo.diretive.ts
```typescript
import { Directive, HostListener } from '@angular/core';

@Directive({
  selector: 'button[counting]',
  host: {
    '(click)': 'onClick($event.target)'
  }
})
class CountClicks {
  numberOfClicks = 0;

  onClick(btn) {
    console.log('button', btn, 'number of clicks:', this.numberOfClicks++);
  }
}
```

#### @HostBinding

HostBinding 是属性装饰器，用来动态设置宿主元素的属性值。

##### HostBinding 装饰器定义
```typescript
/**
 * Type of the HostBinding decorator / constructor function.
 *
 * @stable
 */
export interface HostBindingDecorator {
  (hostPropertyName?: string): any;
  new (hostPropertyName?: string): any;
}

/**
 * Type of the HostBinding metadata.
 *
 * @stable
 */
export interface HostBinding { hostPropertyName?: string; }
```

##### HostBinding 装饰器应用
demo.directive.ts
```typescript
@Directive({selector: '[ngModel]'})
class NgModelStatus {
  constructor(public control:NgModel) {}

  @HostBinding('class.valid') get valid() { return this.control.valid; }
  @HostBinding('class.invalid') get invalid() { return this.control.invalid; }
}
```

demo.component.ts
```typescript
@Component({
  selector: 'app',
  template: `<input [(ngModel)]="prop">`,
})
class App {
  prop;
}
```

#### Host Property Bindings

也可以在指令的 metadata 信息中，设定宿主元素的属性绑定信息，具体示例如下：
demo.directive.ts
```typescript
@Directive({
  selector: '[ngModel]',
  host: {
    '[class.valid]': 'valid',
    '[class.invalid]': 'invalid'
  }
})
class NgModelStatus {
  constructor(public control:NgModel) {}

  get valid() { return this.control.valid; }
  get invalid() { return this.control.invalid; }
}
```

### 测试

如果是测试一个只有内联模板和内联样式的控件，就不需要`async`函数。angular4官网的测试demo如下：
```TypeScript
// banner-inline.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-banner',
  template: '<h1>{{title}}</h1>'
})
export class BannerComponent {
  title = 'Test Tour of Heroes';
}

// banner-inline.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By }              from '@angular/platform-browser';
import { DebugElement }    from '@angular/core';

import { BannerComponent } from './banner-inline.component';

describe('BannerComponent (inline template)', () => {

  let comp:    BannerComponent;
  let fixture: ComponentFixture<BannerComponent>;
  let de:      DebugElement;
  let el:      HTMLElement;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [ BannerComponent ], // declare the test component
    });

    fixture = TestBed.createComponent(BannerComponent);

    comp = fixture.componentInstance; // BannerComponent test instance

    // query for the title <h1> by CSS element selector
    de = fixture.debugElement.query(By.css('h1'));
    el = de.nativeElement;
  });
});
```

#### TestBed测试台

`TestBed`（测试台）是Angular测试工具集中的首要概念。
它创建Angular测试模块（一个`@NgModule`类），我们可以通过调用它的`configureTestingModule`方法来为要测试的类生成模块环境。
其效果是，你可以把被测试的组件从原有的应用模块中剥离出来，把它附加到一个动态生成的Angular测试模块上，而该测试模块可以为这些测试进行特殊裁剪。

这里的元数据对象只是声明了要测试的组件`BannerComponent`。
这个元数据中没有`imports`属性，这是因为：
(a) 默认的测试模块配置中已经有了`BannerComponent`所需的一切，
(b) `BannerComponent`不需要与任何其它组件交互。

在`beforeEach`中调用`configureTestingModule`，以便`TestBed`可以在运行每个测试之前都把自己重置回它的基础状态。

基础状态中包含一个默认的测试模块配置，它包含每个测试都需要的那些声明（组件、指令和管道）以及服务提供商（有些是Mock版）。

#### createComponent 方法

在配置好`TestBed`之后，我们可以告诉它创建一个待测组件的实例。
在这个例子中，`TestBed.createComponent`创建了一个`BannerComponent`的实例，并返回一个组件测试夹具。

`createComponent`方法封闭了当前的`TestBed`实例，以免将来再配置它。
我们不能再调用任何`TestBed`的方法修改配置：不能调用`configureTestingModule`或任何`override`...方法。
如果这么做，`TestBed`就会抛出错误。

#### ComponentFixture、DebugElement 和 query(By.css)

`createComponent`方法返回ComponentFixture，用来控制和访问已创建的组件所在的测试环境。
这个fixture提供了对组件实例自身的访问，同时还提供了用来访问组件的DOM元素的DebugElement对象。

`title`属性被插值到DOM的`<h1>`标签中。 用CSS选择器从fixture的`DebugElement`中`query``<h1>`元素。

query方法接受predicate函数，并搜索fixture的整个DOM树，试图寻找第一个满足predicate函数的元素。

By类是Angular测试工具之一，它生成有用的predicate。 它的`By.css`静态方法产生标准CSS选择器 predicate，与JQuery选择器相同的方式过滤。

#### 测试程序

再每个测试程序之前，Jasmin都一次运行`beforeEach`函数：
```TypeScript
it('should display original title', () => {
  fixture.detectChanges();
  expect(el.textContent).toContain(comp.title);
});

it('should display a different test title', () => {
  comp.title = 'Test Title';
  fixture.detectChanges();
  expect(el.textContent).toContain('Test Title');
});
```

#### 测试带有外部模板的组件

在实际应用中，`BannerComponent`的行为和刚才的版本相同，但是实现方式不同。
它有一个外部模板和CSS文件，通过`templateUrl`和`styleUrls`属性来指定。

```TypeScript
import { Component } from '@angular/core';

@Component({
  selector: 'app-banner',
  templateUrl: './banner.component.html',
  styleUrls:  ['./banner.component.css']
})
export class BannerComponent {
  title = 'Test Tour of Heroes';
}
```

这些测试有一个问题。 `TestBed.createComponent`方法是同步的。
但是Angular模板编译器必须在创建组件实例之前先从文件系统中读取这些值，而这是异步的。
以前测试内联模板时使用的设置方式不适用于外部模板。

`BannerComponent`测试的设置方式必须给Angular模板编译器一些时间来读取文件。
以前放在`beforeEach`中的逻辑被拆分成了两个`beforeEac`h调用。 第一个`beforeEach`处理异步编译工作。

```TypeScript
import { async } from '@angular/core/testing';

// async beforeEach
beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [ BannerComponent ], // declare the test component
  })
  .compileComponents();  // compile template and css
}));
```

`TestBed.compileComponents`方法会异步编译这个测试模块中配置的所有组件。
在这个例子中，`BannerComponent`是唯一要编译的组件。
当`compileComponents`完成时，外部组件和css文件会被“内联”，而`TestBed.createComponent`会用同步的方式创建一个`BannerComponent`的新实例。

> **NOTE**: WebPack用户不用调用compileComponents，因为它会在构建过程中自动内联模板和css，然后执行测试

#### 测试有依赖的组件

组件经常依赖其他服务。

`WelcomeComponent`为登陆的用户显示一条欢迎信息。它从注入的`UserService`的属性得知用户的身份：
```TypeScript
// welcome.component.ts
import { Component, OnInit } from '@angular/core';

import { UserService }       from './model/user.service';

@Component({
  selector: 'app-welcome',
  template: '<h3 class="welcome" ><i>{{welcome}}</i></h3>'
})
export class WelcomeComponent  implements OnInit {
  welcome = '-- not initialized yet --';
  constructor(private userService: UserService) { }

  ngOnInit(): void {
    this.welcome = this.userService.isLoggedIn ?
      'Welcome, ' + this.userService.user.name :
      'Please log in.';
  }
}

// welcome.component.sepc.ts
TestBed.configureTestingModule({
   declarations: [ WelcomeComponent ],
// providers:    [ UserService ]  // NO! Don't provide the real service!
                                  // Provide a test-double instead
   providers:    [ {provide: UserService, useValue: userServiceStub } ]
});
```

##### 提供服务替身

注入真实的service组件有可能很麻烦，而且不能确保能获取到相同的测试对象。
实际上，服务的替身（stubs、fakes、spies或者mocks）通常会更加合适。
所以在真实的`UserService`的位置创建和注册`UserService`替身，会让测试更加容易和安全。

```TypeScript
userServiceStub = {
  isLoggedIn: true,
  user: { name: 'Test User'}
};
```

##### 获取注入的服务

最安全并总是有效的获取注入服务的方法，是从被测试的组件的注入器获取。 组件注入器是fixture的`DebugElement`的属性。

```TypeScript
// UserService actually injected into the component
userService = fixture.debugElement.injector.get(UserService);
```

##### TestBed.get 方法

你可以通过`TestBed.get`方法来从根注入器中获取服务。 它更容易被记住，也更加简介。
但是只有在Angular使用测试的根注入器中的那个服务实例来注入到组件时，它才有效。
幸运的是，在这个测试套件中，唯一的`UserService`提供商就是根测试模块，所以像下面这样调用`TestBed.get`很安全：

```
// UserService from the root injector
userService = TestBed.get(UserService);
```

##### 测试程序

```
beforeEach(() => {
  // stub UserService for test purposes
  userServiceStub = {
    isLoggedIn: true,
    user: { name: 'Test User'}
  };

  TestBed.configureTestingModule({
     declarations: [ WelcomeComponent ],
     providers:    [ {provide: UserService, useValue: userServiceStub } ]
  });

  fixture = TestBed.createComponent(WelcomeComponent);
  comp    = fixture.componentInstance;

  // UserService from the root injector
  userService = TestBed.get(UserService);

  //  get the "welcome" element by CSS selector (e.g., by class name)
  de = fixture.debugElement.query(By.css('.welcome'));
  el = de.nativeElement;
});

it('should welcome the user', () => {
  fixture.detectChanges();
  const content = el.textContent;
  expect(content).toContain('Welcome', '"Welcome ..."');
  expect(content).toContain('Test User', 'expected name');
});

it('should welcome "Bubba"', () => {
  userService.user.name = 'Bubba'; // welcome message hasn't been shown yet
  fixture.detectChanges();
  expect(el.textContent).toContain('Bubba');
});

it('should request login if not logged in', () => {
  userService.isLoggedIn = false; // welcome message hasn't been shown yet
  fixture.detectChanges();
  const content = el.textContent;
  expect(content).not.toContain('Welcome', 'not welcomed');
  expect(content).toMatch(/log in/i, '"log in"');
});
```


### 生命周期

#### `ExpressionChangedAfterItHasBeenCheckedError` error 

constructor -> OnChanges -> onInit -> doCheck -> afterContentInit -> afterContentChecked -> afterViewInit -> afterViewChecked -> onDestroy

angular从app-root开始做变化检查（change detection），检查所有的数据绑定，然后开始检查子组件，
并且其只做一次检查（而不是循环检查直到稳定），父组件的数据绑定将不会再次检查，
所以此时应用处在一个不稳定状态（inconsistent state。

在开发模式中，angular进行两个变化检查，第二次将会确认数据绑定是否发生改变，如果有就抛出`这个`错误。

所以，如果在afterViewInit中修改了数据绑定就会出现这个错误。

**解决方案**：
1. setTimeout / Promise.resolve().then
2. ChangeDetectorRef#detectChanges() 强制检查
3. 如果是对与`*ngIf`的问题，可用`[hidden]`替代

## 参考

1. [[Angular 2] ElementRef, @ViewChild & Renderer](http://www.cnblogs.com/Answer1215/p/5898545.html)
2. [Angular ngFor, &lt;ng-template&gt; and the compiler](https://toddmotto.com/angular-ngfor-template-element)
3. [Angular 2 HostListener & HostBinding](https://segmentfault.com/a/1190000008878888)
4. [Angular4: 文档-核心知识-测试](https://angular.cn/guide/testing#测试)
5. [Everything you need to know about the `ExpressionChangedAfterItHasBeenCheckedError` error](https://blog.angularindepth.com/everything-you-need-to-know-about-the-expressionchangedafterithasbeencheckederror-error-e3fd9ce7dbb4)