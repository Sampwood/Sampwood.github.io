---
title: indexDB的使用
categories:
  - coding
  - js
tags:
  - 浏览器
date: 2019-02-28 14:27:33
update: 2019-02-28 14:27:33
---

### 概述

现代浏览器提供了一些数据存储方案，例如`cookie`、`sessionStorage`、`localStorage`和`indexedDB`。

这些都能在浏览器开发工具的`application`标签页中能看到。

在这里记录下，`indexedDB`的基本用法。

> 通俗地说，`IndexedDB` 就是浏览器提供的本地数据库，它可以被网页脚本创建和操作。
> `IndexedDB` 允许储存大量数据，提供查找接口，还能建立索引。
> 这些都是 `LocalStorage` 所不具备的。
> 就数据库类型而言，`IndexedDB` 不属于关系型数据库（不支持 `SQL` 查询语句），更接近 `NoSQL` 数据库。

<!-- more -->

> IndexedDB 具有以下特点。
> （1）**键值对储存** IndexedDB 内部采用对象仓库（object store）存放数据。所有类型的数据都可以直接存入，包括 JavaScript 对象。对象仓库中，数据以"键值对"的形式保存，每一个数据记录都有对应的主键，主键是独一无二的，不能有重复，否则会抛出一个错误。
> （2）**异步** IndexedDB 操作时不会锁死浏览器，用户依然可以进行其他操作，这与 LocalStorage 形成对比，后者的操作是同步的。异步设计是为了防止大量数据的读写，拖慢网页的表现。
> （3）**支持事务** IndexedDB 支持事务（transaction），这意味着一系列操作步骤之中，只要有一步失败，整个事务就都取消，数据库回滚到事务发生之前的状态，不存在只改写一部分数据的情况。
> （4）**同源限制** IndexedDB 受到同源限制，每一个数据库对应创建它的域名。网页只能访问自身域名下的数据库，而不能访问跨域的数据库。
> （5）**储存空间大** IndexedDB 的储存空间比 LocalStorage 大得多，一般来说不少于 250MB，甚至没有上限。
> （6）**支持二进制储存** IndexedDB 不仅可以储存字符串，还可以储存二进制数据（ArrayBuffer 对象和 Blob 对象）。

### 基本概念

- 数据库：IDBDatabase 对象
- 对象仓库：IDBObjectStore 对象
- 索引： IDBIndex 对象
- 事务： IDBTransaction 对象
- 操作请求：IDBRequest 对象
- 指针： IDBCursor 对象
- 主键集合：IDBKeyRange 对象

### 操作流程

一个基本的`indexDB`流程是这样的：

1. 打开数据库
2. 在这个数据库中创建对象仓库
3. 开启事务，并发起数据库操作请求
4. 监听特定事件，等待操作完成
5. 对返回结果进行处理

#### 打开数据库

```javascript
let db
const require = window.indexedDB.open(databaseName, version)

request.onerror = function (event) {
  console.log('数据库打开报错')
}
request.onsuccess = function (event) {
  console.log('数据库打开成功')
  db = event.target.result
}
request.onupgradeneeded = function (event) {
  console.log('数据库升级成功')
  db = event.target.result
}
```
`window.indexedDB.open`方法接受两个参数，
第一个参数是字符串，表示数据库的名字。如果指定的数据库不存在，就会新建数据库。
第二个参数是整数，表示数据库的版本。
如果省略，打开已有数据库时，默认为当前版本；新建数据库时，默认为1。
如果指定的版本号，大于数据库的实际版本号，就会发生数据库升级事件`onupgradeneeded`。

**Note: 数据库升级成功之后会调用`onsuccess`的回调**

#### 新建对象仓库

新建/删除/更新对象仓库只能在数据库升级的回调函数中，
```javascript
// This event is only implemented in recent browsers   
request.onupgradeneeded = function(event) { 
  // Save the IDBDatabase interface 
  db = event.target.result;

  // Create an objectStore for this database
  const objectStore = db.createObjectStore("name", { keyPath: "myKey" })
};
```

主键（key）是默认建立索引的属性。
比如，数据记录是`{ id: 1, name: '张三' }`，那么`id`属性可以作为主键。
主键也可以指定为下一层对象的属性，比如`{ foo: { bar: 'baz' } }`的`foo.bar`也可以指定为主键。

如果要指定主键为一个递增的整数，可以用
```
db.createObjectStore("name", { keyPath: "myKey", autoIncrement: true })
```

如果要创建索引：
```
objectStore.createIndex('name', 'name', { unique: false });
objectStore.createIndex('email', 'email', { unique: true });
```

#### 开启事务，数据库操作

开启事务的方法是`IDBDatabase.transaction(['person'], 'readwrite')`。
其中`person`是对象仓库名，第二个参数指明读写权限：`'readwrite'/'readonly'`。

开启事务之后通过`IDBTransaction.objectStore(name)`，拿到 `IDBObjectStore` 对象。
在这个对象上调用增删改查的方法。

**Note: 当一次事务需要对数据库进行多次操作的时候，就要监听事务的回调`oncomplete`和`onerror`**
```javascript
// Do something when all the data is added to the database.
transaction.oncomplete = function(event) {
  alert("All done!");
};

transaction.onerror = function(event) {
  // Don't forget to handle errors!
};

const objectStore = transaction.objectStore("customers");
customerData.forEach(function(customer) {
  const request = objectStore.add(customer);
  request.onsuccess = function(event) {
    // event.target.result === customer.ssn;
  };
});
```

##### 增
```javascript
function add() {
  const request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .add({ id: 1, name: '张三', age: 24, email: 'zhangsan@example.com' });

  request.onsuccess = function (event) {
    console.log('数据写入成功');
  };

  request.onerror = function (event) {
    console.log('数据写入失败');
  }
}

add();
```

##### 删
```javascript
function remove() {
  const request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .delete(1);

  request.onsuccess = function (event) {
    console.log('数据删除成功');
  };
}

remove();
```

**Note: delete方法的参数是主键值**

##### 改
```javascript
function update() {
  const request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .put({ id: 1, name: '李四', age: 35, email: 'lisi@example.com' });

  request.onsuccess = function (event) {
    console.log('数据更新成功');
  };

  request.onerror = function (event) {
    console.log('数据更新失败');
  }
}

update();
```

**Note: put方法是按照主键值来进行更新。**

##### 查
```javascript
function read() {
   const transaction = db.transaction(['person']);
   const objectStore = transaction.objectStore('person');
   const request = objectStore.get(1);

   request.onerror = function(event) {
     console.log('查询失败');
   };

   request.onsuccess = function( event) {
      if (request.result) {
        console.log('Name: ' + request.result.name);
        console.log('Age: ' + request.result.age);
        console.log('Email: ' + request.result.email);
      } else {
        console.log('未获得数据记录');
      }
   };
}

read();
```

**Note: get方法的参数是主键值**

#### 索引的使用

索引的意义在于，可以让你搜索任意字段，也就是说从任意字段拿到数据记录。如果不建立索引，默认只能搜索主键（即从主键取值）。

在上面，我们对`name`和`email`建立了索引。
现在，就可以从`name`找到对应的数据记录了。

```javascript
const transaction = db.transaction(['person'], 'readonly');
const store = transaction.objectStore('person');
const index = store.index('name');
const request = index.get('李四');

request.onsuccess = function (e) {
  const result = e.target.result;
  if (result) {
    // ...
  } else {
    // ...
  }
}
```

**Note: 因为'name'索引设置成不是唯一的，所以有可能会出现重名。这种情况只会返回一条数据（key最小的数据）。如果想要获取所有的数据，可以使用下面所说的游标**

#### 游标（cursor）的使用

当需要遍历数据库所有数据的时候，可以使用游标（cursor）：
```javascript
const objectStore = db.transaction("customers").objectStore("customers");

objectStore.openCursor().onsuccess = function(event) {
  const cursor = event.target.result;
  if (cursor) {
    alert("Name for SSN " + cursor.key + " is " + cursor.value.name);
    cursor.continue();
  }
  else {
    alert("No more entries!");
  }
};
```

**Note: 如果只是想获取到所有的数据，可以使用`objectStore.getAll()`或`objectStore.getAllKeys()`**
```javascript
objectStore.getAll().onsuccess = function(event) {
  alert("Got all customers: " + event.target.result);
};
```

同样，索引也可以使用游标:
```javascript
// 使用普通的游标，会获取整个数据对象
index.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // cursor.key is a name, like "Bill", and cursor.value is the whole object.
    alert("Name: " + cursor.key + ", SSN: " + cursor.value.ssn + ", email: " + cursor.value.email);
    cursor.continue();
  }
};

  // 使用key游标，会获取数据对象的key
index.openKeyCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // cursor.key is a name, like "Bill", and cursor.value is the SSN.
    // No way to directly get the rest of the stored object.
    alert("Name: " + cursor.key + ", SSN: " + cursor.primaryKey);
    cursor.continue();
  }
};
```

## 参考
1. [浏览器数据库 IndexedDB 入门教程](http://www.ruanyifeng.com/blog/2018/07/indexeddb.html)
2. [Using IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB)