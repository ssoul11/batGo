### 一、flex布局

任何容器都可以指定为flex布局

- 当父盒子设为flex布局以后，子元素的float、clear和vertical-align属性都将失效

1.**flex-direction**

设置主轴的方向。当flex以列为方向时，`justify-content`控制纵向对齐（当你调转行或列的方向后，flex-start和flex-end对应的方向也被调转了），`align-items`控制横向对齐

- `row`: 元素摆放的方向和文字方向一致。（默认从左到右）
- `row-reverse`: 元素摆放的方向和文字方向相反。
- `column`: 元素从上放到下。
- `column-reverse`: 元素从下放到上。

2.**justify-content**

设置主轴子元素排列（使用之前先确定主轴）

- `flex-start`: 元素和容器的从头对齐。
- `flex-end`: 元素和容器的尾部对齐。
- `center`: 元素在容器里居中。
- `space-between`:先贴两边，再平均分配。
- `space-around`:平均分配。



3.**flex-wrap**

定义flex元素必须在单行或自动换行成多行。

- `nowrap`: 所有的元素都在一行。
- `wrap`: 元素自动换成多行。
- `wrap-reverse`: 元素自动换成逆序的多行。



4.**align-content**

设置侧轴子元素排列方式（多行）。当交叉轴有多余空间时，对齐容器内的轴线。`align-content`决定行之间的间隔，而`align-items`决定元素整体在容器的什么位置。只有一行的时候`align-content`没有任何效果。

- `flex-start`: 多行都集中在顶部。
- `flex-end`: 多行都集中在底部。
- `center`: 多行居中。
- `space-between`: 行与行之间保持相等距离。
- `space-around`: 每行的周围保持相等距离。
- `stretch`: 每一行都被拉伸以填满容器。



5.**align-items**

设置侧轴子元素排列方式（单行）。

- `flex-start`: 元素与容器的顶部对齐。
- `flex-end`: 元素与容器的底部对齐。
- `center`: 元素纵向居中。
- `baseline`: 元素在容器的基线位置显示。
- `stretch`: 元素被拉伸以填满整个容器。

- 

6.**order**

有时候仅仅调转行或列的方向是不够的。在这些情况，我们可以设置单个元素的`order`属性。元素的属性默认值为0，但是我们设置这个属性为正数或负数。（通过0、正数、负数调整顺序）

7.**align-self**

单独纵向对齐

在交叉轴上对齐一个元素，覆盖`align-items`所设定的值`flex-start` `flex-end` `center` `baseline` `stretch`。接受和`align-items`一样的那些值。



8.**flex-flow**

flex-direction与flex-warp的结合，中间空格隔开，**flex-flow: row wrap**