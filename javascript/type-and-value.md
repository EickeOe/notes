---
description: JavaScript小记-类型
---

# 类型和值



### 类型

* js中有7个内置类型：null，undefined，Boolean，number，string，object，symbol
* 变量没有类型，但是变量的值有类型。这些类型定义了值的固有行为
* 在js中，_undefined_是指声明了一个可以存放值的变量，只是当前没有值。_undeclared_ 则是指一个没有被声明的变量
* typeof操作符的安全保护（防止错误抛出）机制，undefined和_undeclared_ 在typeof的返回值上都返回undefined，在某些特定情况下检测没有生命过的变量很有帮助

### 值

* 在JavaScript中，数组是数字索引的集合，能够存放任意类型的值.字符串有点类数组，但是他们之间又截然不同的行为，当你把它当作数组来对待的时候，你需要特别小心。JavaScript中的数字包括“整数”和浮点数值。
* 基本类型中定义了几个特殊值。
  * `null`类型只有一个值：`null`，与`undefined`一个值一样。`undefined`是任何没有被赋值的变量或属性的默认值。`void`操作符能够从任何其他值中创建`undefined`。
  * `number`包括几个特殊值，比如`NaN`（所谓的"Not aNumber"，实际上更恰当的理解是"invalid number"）；`+Infinity`和`Infinity`；以及`-0`。
  * 简单原始类型（`string`,`number`等）通过值拷贝进行赋值或传递，但复合值（`object`等）是通过引用拷贝进行赋值或传递。JS中的引用并不像其他语言中的引用或指针--他们永远不指向其他变量或引用，仅指向底层的值。

### 类型和语法

* `Function`和`Array`和`RegExp`的`.prototype`是已经创建和内置的
  * `Function.prototype=()=>{}`
  * `Array.prototype=[]`
  * `RegExp.prototype.toString()="/(?:)/"`
* JavaScript为原始值提供对象包装类，被称为natives（String，Number，Boolean等）。这些对象包装器能够让原始值访问每个对象的子类型对应的行为（`String#trim()`和`Array#concat(...)`）。
* 如果你有一个像`"abc"`这样的简单原始类型值，当你访问它的`length`属性或一些`String.portotype`的方法，JS会自动**装箱**这个值（把它封装在对应的封装类中），这样你才能访问这些属性和方法。

