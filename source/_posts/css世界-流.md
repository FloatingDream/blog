---
title: css世界--流
date: 2020-03-12 17:32:34
tags:
- html
- css
- 前端
categories:
- 前端技术
thumbnail: /gallery/css世界-流.jpeg
---
本书主要基于css2进行讲解，本书除了对于一些属性的讲解，最重要的部分就是对于css流的介绍。在一开始学习了解css的时候我们就知道css的基础就是“流”，但是当时对于流的理解是css自身的一种属性。但是通过本书可以了解到流也是由来和作用。

## 流体布局
所谓“流体布局”，指的是利用元素“流”的特性实现的各类布局效果，因为“流”本身具有自适应性，所以“流体布局”往往都是具有自适应性的。但是，“流体布局”并不等于“自适应布局”

### 无宽度
对于块状元素，如果width:auto,则元素会如流水一样充满整个容器，而一旦设定了width具体数值，则元素的流动性会消失。如果宽度作用在content box上，内外流动性皆无。

### CSS流体布局下的宽度分离原则
所谓“宽度分离原则”，就是css中width属性不与影响宽度的padding/border（有时候包括margin）属性共存。
如下：
- 不要出现这种写法
```css
.box { width:100px; border: 1px solid; padding:20px;} 
```
- 鼓励这种写法(分离 width 独立占用一层标签，padding\border\margin利用流动性在内部自适应)
```css
.father {
  width:142px;
}
.son {
  padding:20px;
  border:1px solid;
}
```
思想：
便于维护，盒子尺寸中4个属性都能影响到宽度，因此改动需要非常小心精确算好对于宽度的影响。宽度分离后，宽度在外层单独固定。内部调整padding\margin\border相互自适应调整（流体）依旧充满整个父元素空间。如果不考虑替换元素，所有页面基本只需要一个width设定就可以了，内部元素根据流体填充。

### *{box-sizing:border-box} vs 宽度分离
border-box 无法解决所有问题。box-sizing 不支持margin-box，只有元素没有水平margin的时候，box-sizing才能和我们预想中一样，宽度分离可以使用所有情况。
而且 box-sizing 的初衷是解决替换元素的宽度自适应问题。

### 无宽度业务运用

通用：
1. 如必须使用 width 属性数值使用比例数值

左右两栏布局
1. position:absolute + padding-left
2. float:left + padding-left

多栏布局
1. column-width + 百分比

总结：宽度分离---充分利用流动性用内部元素充满父元素，不要用具体数值限制非直接内容元素的宽度，让他们被具体内容填充。具体的排版使用padding\margin\float\positon 等属性进行调整。充分利用子元素填充支撑父元素。
   
