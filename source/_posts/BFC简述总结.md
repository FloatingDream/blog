---
title: BFC简述总结
date: 2020-03-18 12:02:56
tags:
- 前端
- CSS
- BFC
categories:
- 前端
thumbnail: /gallery/BFC简述总结.jpg
---
## 定义
BFC(block formatting context),中文“块级格式化上下文”，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。如果一个元素具有BFC,内部元素无法影响到外部元素；外部元素也无法影响内部。类似一块具有结界的元素或则一个单独的文档流。

在块格式化上下文中，盒子一个接一个地垂直排列，从包含块的顶部开始。两个同胞框之间的垂直距离由“margin”属性决定。块格式上下文中相邻块级框之间的垂直边距折叠。

在块格式设置上下文中，每个框的左外边缘与包含块的左边缘相接触(对于从右到左的格式设置，右边缘相接触)。即使在浮动存在的情况下也是如此(尽管一个盒子的行盒可能会因为浮动而收缩)，除非盒子建立一个新的块格式上下文(在这种情况下，盒子本身可能会因为浮动而变窄)。

注意：BFC属于正常文档流，floats、绝对定位元素、不是块状盒子的块状容器（例如：inline-block、table-cell、table-caption），以及其他情况，会为其内容创建新的的BFC。

所以之前常见元素之间能相互影响的情况：
- margin重叠---影响外部元素
- 浮动---直接影响父元素的高度，间接影响其他元素的布局

都可以借助**新建**BFC进行处理

## 触发条件

- `<html>`根元素
- 浮动元素---float 的值不是none
- overflow---值不为 visible 的块元素
- display 的值不是table-cell\table-caption和inline-block中的任何一个
- 绝对定位元素---元素的 position 为 absolute 或 fixed
- 弹性元素---display为 flex 或 inline-flex元素的直接子元素
- 网格元素---display为 grid 或 inline-grid 元素的直接子元素
- 多列容器---元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1
- column-span 为 all 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中

## BFC应用

### margin 重叠
```html
<body>
  <div style="width: 200px;height: 200px;background-color: red;margin: 20px;"></div>
  <div style="width: 200px;height: 200px;background-color: green;margin: 20px;"></div>
</body>
```
重叠示例：
![margin重叠.gif](https://i.loli.net/2020/03/18/MaeRC5YOhQzmNAk.gif)

两个块状元素上下margin出现重叠，如果要避免重叠根据上面BFC的介绍，我们可以新建一个BFC区域。

```html
<body>
  <div style="width: 200px;height: 200px;background-color: red;margin: 20px;over"></div>
  <div style="width: 200px;height: 200px;background-color: green;margin: 20px;"></div>
</body>
```

创建新的BFC消除影响，原理：BFC不会被其他元素的margin影响

```html
<body>
  <div style="overflow: hidden;">
    <div style="width: 200px;height: 200px;background-color: red;margin: 20px;"></div>
  </div>
  <div>
    <div style="width: 200px;height: 200px;background-color: green;margin: 20px;"></div>
  </div>
</body>
```

![消除marin重叠.png](https://i.loli.net/2020/03/18/tRz3v1h49oNx7b6.png)

### 清除浮动（阻止元素被浮动元素覆盖）

```html
<body>
  <div style="width: 200px;height: 200px;float: left;background-color: blueviolet;"></div>
  <div style="width: 200px;height: 200px;background-color: brown;"></div>
</body>
```

紫色下面还有一个大小完全一致的深红色的盒子被浮动的盒子完全覆盖

![浮动元素覆盖.png](https://i.loli.net/2020/03/18/IlDz4nAVT1CkoOw.png)

```html
<body>
  <!-- <div style="overflow: hidden;"> -->
    <div style="width: 200px;height: 200px;float: left;background-color: blueviolet;"></div>
  <!-- </div> -->
  <div style="overflow: hidden;">
    <div style="width: 200px;height: 200px;background-color: brown;"></div>
  </div>
</body>
```

将被覆盖元素用新建的BFC包裹，原理：浮动元素是一个BFC和新建的BFC按普通文档流依次排列

![防止被浮动元素覆盖.png](https://i.loli.net/2020/03/18/SLkbhG9eadKUDQN.png)

### 清除浮动（BFC包含浮动元素）

```html
<body>
<div style="width: 200px;height: 200px;float: right;background-color: blueviolet;"></div>
<div style="width: 200px;height: 200px;background-color: brown;"></div>
</body>
```

![浮动](https://i.loli.net/2020/03/18/AXcnhxvo9tNwGBI.png)

影响外部div元素位置上移

```html
<body>
  <div style="overflow: hidden;">
    <div style="width: 200px;height: 200px;float: right;background-color: blueviolet;"></div>
  </div>
    <div style="width: 200px;height: 200px;background-color: brown;"></div>
</body>
```

将浮动元素用一个新建的BFC包裹，但是不是使用浮动建立的。新BFC回归文档流，float元素在新BFC中浮动，无法出来。

![清除浮动](https://i.loli.net/2020/03/18/JmtgufkjebOp2z3.png)


### 自适应布局

- BFC 与 float
  
  相对纯流体布局，自适应内容自动填满浮动以外区域。无需考虑浮动元素的宽度；无需考虑margin和padding撑开间距。

可以考虑抽象几个通用的布局类名：
- ```.left { float:left; }```
- ```.right { float:right; }```
- ```.bfc { overflow:hidden; }```

由于创建BFC的元素都比较特殊，有其自己的属性，一般建议使用 
- overflow:heidden
- 融合display:table-cell 和 display:inline-block
  ```css
  .lbf-content{
    display:tabel-cell;width:9999px;
    // 兼容IE7
    display:inlinte-block;
    width:auto;
  }
  ```



## 总结
BFC 类似于在一个文档流中又创建了一个文档流；日常中元素会相互影响，但是文档流不会；因此可以利用新建文档流包裹元素来避免影响。

参考：[css2.2](https://www.w3.org/TR/CSS22/visuren.html#block-formatting)


