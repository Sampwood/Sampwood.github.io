### Enums

TypeScript提供数字和基于字符串的枚举。

#### 数字枚举

```
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}
```

上述枚举对象，首先定义了`up`为1，那么下面的每个元素都基于其上面一个元素自增1。如此`Down`是2，
`Left`是3，`Right`就是4。

当然，如果连`up`都没有设置数值，那么`up`默认就为**0**。