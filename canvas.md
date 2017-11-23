---
layout: default
---

# [](#header-3)Canvas画图并生成对应的图片一些坑与相关的经验~
## [](#header-4)基础部分：
### [](#header-5)在html中加入一个canvas标签
```html
<canvas id="mycan" width="629" height="1145" class="hide"></canvas>
```
注意：记得要定义width和height属性，因为最后生成出来的图片尺寸就是由这里进行控制，不过如果需要实时显示canvas里面内容的话canvas的宽高还需要对应的适配屏幕，不需要实时显示的话直接隐藏元素就行
### [](#header-5)Js中获取canvas标签并创建画布
```js
var canvas= document.getElementById('mycan');
var context = canvas.getContext("2d");
```
获取canvas标签并且在canvas上创建2d的画布

[back](./)
