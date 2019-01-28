# position：sticky粘性布局



### position：sticky粘性布局

#### 作用

* 它是相对定位`position:relative`和固定定位`position:fixed`的混合。在屏幕范围时该元素并不受到定位的影响（left、top等属性），当该元素将要滚动到某一阈值点\(例：`top:10px;`\)，根据left、top等属性成固定定位`fixed`的效果。
* 不再用监听scroll事件，既可实现导航栏滚动绝对定位的效果

#### 生效条件

* 父元素不能`overflow:hidden`或者`overflow:auto`属性
* 必须指定`top`、`bottom`、`left`、`right`4个值之一，否则只会处于相对定位
* 父元素的高度不能低于sticky元素的高度
* sticky元素仅在其父元素内生效

> 注意：该元素不脱离文档流，仍然保留元素原本在文档流中的位置

#### 兼容性

* IE11
* edge
* chrome
* firxfox
* safari

> Q: 如何兼容IE11？
>
> A: 使用`position: fixed;`,或者使用[sticky填充包](https://github.com/dollarshaveclub/stickybits)
>
> Q: Safari中无效如何解决？
>
> A: 使用`posititon :-webkit-sticky;`

#### 能够解决哪些问题？

* 当元素使用`transform`属性时，会限制`position:fixed`的跟随效果，这时可以使用`position:sticky`来兼容`fixed`定位

  > 这个影响只出现在chrome和firefox中

