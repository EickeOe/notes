# THREE笔记

## 半球光（hemisphere light）

半球光是渐变光，光照颜色从天空光颜色渐变到地面光颜色

> 注意：半球光不能让物体产生阴影，让物体产生引用请使用平行光（directional light）

构造器：HemisphereLight\( skyColor : Integer, groundColor : Integer, intensity : Float \)

参数：

* skyColor：天空发出的光线颜色，默认值为0xffffff
* groundColor：地面发出的光线颜色，默认值为0xffffff
* intensity：光照强度，默认值为1

## 平行光（directional light）

平行光是沿着特定方向发射的光（又叫定向光）。这种光的表现就像太阳光，无限远，光线都是平行的

构造器：DirectionalLight\( color : Integer, intensity : Float \)

参数：

* color：光的颜色，默认值为0xffffff
* intensity：光照强度，默认值为1

