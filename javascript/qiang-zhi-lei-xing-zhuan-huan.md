# JSON.stringify详解

当我们使用`JSON.stringify()`序列化一个值为JSON字符串，只有**JSON安全的值**才可以通过`JSON.stringify()` 转换为字符串。那么，什么是**JSON安全的值**？能够有效用JSON形式表示的任何值。

非JSON安全的值，例如：`undefined`、`function`和`symbol`以及循环引用的object（对象结构的属性引用通过创建彼此，构成一个无限循环）。对一个标准的JSON结构来说，这些值都是非法的，主要是因为它们无法移值到其他支持JSON的语言中。

当`JSON.stringify()`遇到`undefined`、`function`和`symbol`的时候，会自动忽略它们的值，如果这样的值在数组中会被替换成`null`（这样数组的索引信息就不会被改变）。如果这样的值出现在对象的属性中，那这个属性会被排除掉。

```javascript
JSON.stringify( undefined );					// undefined
JSON.stringify( function(){} );					// undefined

JSON.stringify( [1,undefined,function(){},4] );	// "[1,null,null,4]"
JSON.stringify( { a:2, b:function(){} } );		// "{"a":2}"
```

{% hint style="info" %}
`JSON.stringify()格式化一个循环引用的对象，会抛出异常`
{% endhint %}

JSON字符串有一个特殊的行为，如果一个对象定义了`toJSON()`方法，那这个方法先被调用，并且将它返回的值进行序列化。

如果将一个含有非法JSON值的对象序列化成JSON字符串，需要定义一个`toJSON()`方法，用于返回该对象的JSON安全版本。

```javascript
var o = { };

var a = {
	b: 42,
	c: o,
	d: function(){}
};

// create a circular reference inside `a`
o.e = a;

// would throw an error on the circular reference
// JSON.stringify( a );

// define a custom JSON value serialization
a.toJSON = function() {
	// only include the `b` property for serialization
	return { b: this.b };
};

JSON.stringify( a ); // "{"b":42}"
```

{% hint style="info" %}
常见的误解是toJSON\(\)应该返回一个对象的JSON字符串。这是错误的，toJSON应该返回一个任何类型的正常值，并且JSON.stringify\(\)本身会将这个值进行序列化。
{% endhint %}

JSON.stringify\(\)第二个参数是个可选参数，被称为replacer。这个参数可以是数组或者函数。它提供了一个过滤机制制定了应该或者不应该包含哪些属性，从而实现了对象可定制的递归序列化，这种做法和toJSON\(\)如何序列化准备一个值很相似。

如果replacer是一个数组，它应该是一个字符串数组，每一个值都指定了对象的属性名称，代表属性应该被加入到序列化中，如果一个属性不在这个列表中，它会被跳过。

如果replacer是一个函数，它首先会被该对象调用一次，然后该对象的每个属性会调用一次，每次都会给这个函数传递两个值，key和value。想在序列化过程中跳过某个key，只需要返回undefined，否则返回提供的value。

```javascript
const obj = {
    a: 0,
    b: 1,
    c: [0, 1, 2]
}
JSON.stringify(obj,['a', 'c']) // "{"a":0,"c":[0,1,2]}"

JSON.stringify(obj,(key,value)=>{
	if(key !== 'b'){
		return value
	}
})
// "{"a":0,"c":[1,2,3]}"
```

{% hint style="info" %}
在`replacer`为函数时，第一次调用时，会把`obj`对象自身传递进去。`if`语句过滤掉名称为`"b"`的属性。字符串是递归进行的，所以数组`[1,2,3]`中的每个值都被传递给**replacer**的参数`value`，对应的下标`(0,1,2)`作为参数`key`。
{% endhint %}

第三个可选的参数也可以传递给JSON.stringify\(\)，被称为space，被用作缩进，是为了使得输出有格式更加漂亮。space可以是一个正整数，指定每层缩进应该缩进多少个空格。或者space也可以是个字符串，其值的前十个字符被用于每个缩进层次。

```javascript
const a = {
	b: 42,
	c: "42",
	d: [1,2,3]
};

JSON.stringify( a, null, 3 );
// "{
//    "b": 42,
//    "c": "42",
//    "d": [
//       1,
//       2,
//       3
//    ]
// }"

JSON.stringify( a, null, "-----" );
// "{
// -----"b": 42,
// -----"c": "42",
// -----"d": [
// ----------1,
// ----------2,
// ----------3
// -----]
// }"
```



