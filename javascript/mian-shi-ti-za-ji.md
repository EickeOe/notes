# 面试题杂记

## JavaScript

Q：说一下你了解到的ES6包括后续版本的新特性

A：

* 新的数据类型`Symbol`&#x20;
* 新的声明方式`let`和`const`
* 解构赋值（数组解构和对象解构）
* 新的数据结构（`Set`和`Map`）
* 字符串、正则、数值、函数、对象的扩展
* `Lambda`表达式（箭头函数）
* `Proxy`代理对象
* `Promise `对象
* `Iterator`（迭代器）
* `Generator`（生成器）函数
* `async`和`await`
* `Class`类的面向对象

Q：解构赋值，看下面的代码说出a、b、c、e、f、g的值

```javascript
let [a, b, ...c] = [0, 1, 2]
let {e, f, ...g} ={e: 4, f: 5, g: 6}
```

A：a=0, b=1, c=\[2], e=4, f=5, g={g: 6}
