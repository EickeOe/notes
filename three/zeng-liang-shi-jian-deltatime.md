# 增量时间（deltaTime）

### 描述

增量时间是根据上一帧更新游戏以来经过的时间，用于动态的更新游戏场景渲染。

### 基于时间的动画

在早期游戏开发中，一些游戏的场景移动速度依赖于CPU的运行速度，游戏场景将每帧在屏幕上移动一定数量的像素。如果CPU只能以每秒15帧（FPS）运行游戏，并且游戏场景被代码定义为每秒向左移动10个像素，则游戏场景将以每秒150个像素向左移动。但是，如果性能更好的处理器可以每秒60帧运行游戏呢？游戏场景将以600像素移动！怎么能解决这个问题呢？

解决这个问题的关键是记录自上一帧以来经过的时间。这个时间通常叫做增量时间（deltaTime）。只要我们拿到了增量时间，我们就可以通过使用下面的公式计算出我们的游戏场景应该移动此帧的距离：

```javascript
const distance = speed * deltaTime
```

以上三个变量：

* distance：移动当前帧的像素数
* speed：每秒像素的速度
* deltaTime：上一帧以来经过的时间

示例：

```javascript
const APP.core = {
 frame() {
 APP.core.setDelta()
 APP.core.update()
 APP.core.render()
 APP.core.animationFrame = window.requestAnimationFrame(APP.core.frame)
 },

 setDelta() {
 APP.core.now = Date.now()
 APP.core.delta = (APP.core.now - APP.core.lastTime ) / 1000 // 上一帧以来经过的秒数
 APP.core.lastTime = APP.core.now
 },

 update() {
 // 更新值
 // var distance = 100 * APP.core.delta
 // APP.thing.x += distance
 },

 render() {
 // 渲染更新
 }
}
```

