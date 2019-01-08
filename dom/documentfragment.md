---
description: DOM的DocumentFragment对象相关笔记
---

# DocumentFragment

{% hint style="info" %}
DocumentFragment接口可以用来做dom级别的性能优化
{% endhint %}

## 介绍

DocumentFragment 接口表示一个没有父级文件的最小文档对象。它被当做一个轻量版的 Document 使用，用于存储已排好版的或尚未打理好格式的XML片段。最大的区别是因为DocumentFragment不是真实DOM树的一部分，它的变化不会引起DOM树的重新渲染的操作\(reflow\) ，且不会导致性能等问题。

## 如何使用

常见的使用方法是创建一个DocumentFragment 对象，在对象中组装一个DOM子树，然后使用[Node](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)对象\(例如document.body\)的`appendChild`或者`insertBefore`方法将该片段附加或插入到DOM中。

这种情况下被添加\(append\)或者被插入\( inserted\)的是DocumentFragment 对象的所有子节点，而非DocumentFragment 对象本身，DocumentFragment 对象变成空白DocumentFragment 对象。

> 这使得DocumentFragment 对象成了有用的占位符，暂时存放那些一次插入document的节点，有利于实现document的剪切、复制、粘贴操作。

因为所有的节点会被一次插入到document中，而这个操作仅发生一个重渲染的操作，而不是每个节点分别被插入到文档中\(会发生多次重渲染的操作\)。

```javascript
const frag = document.createDocumentFragment()
for (let i = 0; i < 100; i++) {
  const op = document.createElement('P')
  const oText = document.createTextNode(i)
  op.appendChild(oText)
  frag.appendChild(op)
}
document.body.appendChild(frag)
```

{% hint style="warning" %}
当DocumentFragment 对象插入完会自动销毁DocumentFragment 对象的内容
{% endhint %}

#### 移除DOM到DocumentFragment对象中

如果将document中的节点添加到DocumentFragment对象中，就会从document中移除该节点，也不会从浏览器中看到该节点，添加到DocumentFragment对象的新节点也不属于DOM树

```markup
​<html>
<head>
  <meta charset="UTF-8">
</head>
<body>
<div>
    <ul>
        <li>1</li>
        <li>2</li>
    </ul>
</div>
    <script type="text/javascript">
       var frag=document.createDocumentFragment();
       var ul = document.getElementsByTagName('ul')[0]
       console.log(ul)
       frag.appendChild(ul)
    </script>
</body>
</html>
```

把DOM树中的ul节点添加到文档碎片中，这时dom树中的ul节点也消失了，存放在DocumentFragment对象中了，所有的操作不会重渲染dom树

