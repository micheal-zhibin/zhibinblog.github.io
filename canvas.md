---
layout: default
---

# [](#header-1)Canvas画图并生成对应的图片一些坑与相关的经验~

## [](#header-2)基础部分：

### [](#header-3)a)	在html中加入一个canvas标签
```html
<canvas id="mycan" width="629" height="1145" class="hide"></canvas>
```
注意：记得要定义width和height属性，因为最后生成出来的图片尺寸就是由这里进行控制，不过如果需要实时显示canvas里面内容的话canvas的宽高还需要对应的适配屏幕，不需要实时显示的话直接隐藏元素就行

_yay_

[back](./)
