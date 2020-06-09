# CSS

**css的世界诞生就是为图文信息展示服务的**

## 块级元素

**block**

实际由外在的“块级盒子”和内在的“块级容器盒子”组成，

**inline-block** 

外在的“内联盒子”和内在的“块级容器盒子”组成，值为 inline 的元素则内外均是“内联盒子”

**width-height**作用在容器盒子

在页面丢一个div进去他就会像水一样铺满容器

页面元素主要为



## 滚动界限种类

### Scroll Snap

 CSS Scroll Snap是个非常好用的特性，可以让网页容器滚动停止的时候，无需任何JS代码的参与，浏览器可以自动平滑定位到指定元素的指定位置。类似幻灯片广告效果就可以纯CSS实现。 

```css
.scroll_y {
    width: 100%;
    height: 9999px;
    position: absolute;
    display: none;
  }
  .scroller {
    height: 300px;
    overflow-y: scroll;
    scroll-snap-type: y mandatory;
  }
  .scroller .scrollers {
    height: 100%;
    scroll-snap-align: start;
  }
```



### scroll0snap-type

`scroll-snap-type`作用就是确定是水平滚动定位，还是垂直滚动定位。支持的属性值如下：

- **none**

  默认值。表示滚动时候忽略捕捉点，也就是我们平时使用的滚动。

- **x**

  捕捉水平定位点。

- **y**

  捕捉垂直平定位点。

- **block**

  捕捉和块状元素排列一个滚动方向的定位点。默认文档流下指的就是垂直轴。

- **inline**

  捕捉和内联元素排列一个滚动方向的定位点。默认文档流下指的就是水平轴。

- **both**

  横轴纵轴都捕捉。

- **mandatory**

  mandatory表示“强制”，可选参数。强制定位，无论是添加删除元素，或者滚动窗口较小，不足以放下子元素。

- **proximity**

  proximity表示“大约”，可选参数。可能会定位。这个值的作用表现比较玄乎，时间有限，我也没有弄透。大家可以对比下面这个demo，感受和`mandatory`属性值的不同。

### scroll-snap-stop

`scroll-snap-stop`表示是否允许滚动容器忽略捕获位置。

其中，支持的属性值：

- **normal**

  默认值。可以忽略捕获位置。

- **always**

  不能忽略捕获位置。且必须定位到第一个捕获元素的位置。

这个属性很有用，可以保证我们每次只能滚动一屏，或者一个指定元素，而不会一下子滚多屏或多个元素，只可惜，根据我的测试，还没有浏览器支持（虽然属性合法，但并无行为发生）。

###  scroll-snap-align

`scroll-snap-align`是作用在滚动容器子元素上的，表示捕获点是上边缘，下边缘，还是中间位置。

其中，支持的属性值：

- **none**

  默认值。不定义位置。

- **start**

  起始位置对齐，例如，垂直滚动，子元素和容器同上边缘对齐。

- **end**

  结束位置对齐，例如，垂直滚动，子元素和容器同下边缘对齐。

- **center**

  居中对齐。子元素中心和滚动容器中心一致。



## 常用a t规则及使用示例

### @charset

用于提示css文件使用的字符串编码方式，它如果被使用，必须出现在最前面。这个规则只是在给出语法解析阶段前使用，并不影响页面上展示效果。

```
@charset "utf-8"
```



### @import

用于引入一个css文件，除@charset规则不会被引入外，可以引入一个文件的全部内容。

```
@import "mystyle.css";
@import url("mystyle.css");
@media
media query 使用规则，它能够对设备的类型进行一些判断，在media的区块中，是普通规则列表。

@media print {
    body {
        font-size: 10pt;
    }
}
```



### @page

page用于分页媒体访问网页时的表现设置，页面是一种特殊的盒模型结构，除了页面本身，还可以设置它周围的盒。

```
@page {
    size: 8.5in 11in;
    margin: 10%;
    @top-left {
        content: "Hamlet";
    }
    @top-right {
        content: "Page " counter(page);
    }
}
```



### @counter-style

counter-style产生一种数据，用于定义列表项的表现。

```
@counter-style triangle {
    system: cyclic;
    suffix: "";
}
```



### @key-frames

key-frames产生一种数据，用于定义动画关键帧。

```
@keyframes diagonal-side {
    from {
        left: 0;
        top: 0;
    }
    

    to {
        left: 100px;
        top: 100px;
    }

}
```



### @fontface

fontface用于定义一种字体，iconfont技术就是利用这个特性来实现。

```
@font-face {
    font-family: Gentium;
    src: url(http://xxxx);
}
p {
    font-family: Gentium, serif;
}
```



### @support

support检查环境的特性，它与media比较类似。

### @namespace

用于跟xml命名空间配合的一个规则，表示内部的css选择器全都带上特定命名空间。

### @viewport

用于设置视口的一些特性，不过兼容性目前不是很好，多数时候被html的meta代替。

## 盒模型

- ie盒模型或怪异盒模型（box-sizing：border-box）——content的内容包括padding和border和content

- w3c盒模型（box-sizing：content-box）——width宽度只包含元素的content宽度，总宽度 = width + padding(左右) + border(左右)

## BFC（块级格式上下文）

是一个独立的渲染区域，BFC内部元素与外部元素互相隔离

触发条件

- 根元素
- position：fixed/absolute（脱离文档流)
- float(脱离文档流)
- ovevflow不为visible
- ie浏览器通过zoom：1

通常用来

- 清除浮动
- 自适应两栏布局（左边浮动，右边设置overflow为hidden触发BFC）
- 阻止`margin`重叠

## 居中布局

- 水平居中
  1. 行内元素：text-align：center
  2. 块级元素：margin：0 auto
  3. flex：justify-content：center
  4. absolute-transform
- 垂直居中
  1. lineheight：height
  2. absolute-transform
  3. flex：align-content：center
  4. table

- 垂直水平居中
  1. absolute-transform
  2. flex + justify-content + align-items

4.选择器优先级

!important>行内样式（style）>id#>class.>*>全局样式>继承

## 去除浮动

1. 使用BFC
2. 增加伪元素（：after：clear：both）
3. 父级元素设置行高

## css3新属性

- 边框

  border-radius：圆角边框，border-radius:25px

  box-shadow：边框阴影，box-shadow: 10px 10px 5px#888888

  border-image：边框图片，border-image:url(border.png) 30 30 round;

- 文本效果

  text-shadow：向文本应用阴影，可以规定水平阴影、垂直阴影、模糊距离，以及阴影的颜色

  word-wrap：允许文本进行换行。word-wrap:break-word

- 转换（**transform**）

  translate()：元素从其当前位置移动，根据给定的 left（x 坐标） 和 top（y 坐标） 位置参数。 transform: translate(50px,100px);

  rotate()：元素顺时针旋转给定的角度。允许负值，元素将逆时针旋转。transform: rotate(30deg);

  scale()：元素的尺寸会增加或减少，根据给定的宽度（X 轴）和高度（Y 轴）参数。transform: scale(2,4);

  skew()：元素翻转给定的角度，根据给定的水平线（X 轴）和垂直线（Y 轴）参数。transform: skew(30deg,20deg);

  matrix()： 把所有 2D 转换方法组合在一起，需要六个参数，包含数学函数，允许您：旋转、缩放、移动以及倾斜元素。transform:matrix(0.866,0.5,-0.5,0.866,0,0);

- 过度（**transition**）

  transition-property ：执行动画对应的属性，例如 color，background 等，可以使用 all 来指定所有的属性。

  transition-duration：过渡动画的一个持续时间。

  transition-timing-function：在延续时间段，动画变化的速率，常见的有：ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier 。

  transition-delay：延迟多久后开始动画。

  简写为：transition: [<transition-property> || <transition-duration> || <transition-timing-function> || <transition-delay>];

- 动画（animation）



## 伪类选择器

- +：获取当前元素的相邻的满足条件的元素
- ~：获取当前元素满足条件的兄弟元素
- :first-child :选取属于其父元素的首个子元素的指定选择器
- :last-child :选取属于其父元素的最后一个子元素的指定选择器
- :nth-child(n) ： 匹配属于其父元素的第 N 个子元素，不论元素的类型
- :nth-last-child(n) ：选择器匹配属于其元素的第 N 个子元素的每个元素，不论元素的类型，从最后一个子元素开始计数。
  n 可以是数字、关键词或公式,even偶数，odd奇数
- :first-of-type ：选择属于其父元素的首个元素的每个元素
- :last-of-type ：选择属于其父元素的最后元素的每个元素
- :nth-of-type ：选择属于其父元素第n个元素的每个元素

## 伪元素

- ::before
- ::after
- ::first-letter——选中块级元素第一行的第一个字母，最前面为图片或内联的表格时会干扰选择
- ::first-line——选中块级元素中第一行。
- ::selection——选中被用户高亮选择的部分；可以不配合其他元素使用，只有一小部分的 CSS 属性可用于此选择器：

## flex（弹性布局）

使用display:flex设置，对盒子的属性如下

- ### flex-direction

  决定主轴的方向，

  ```css
    flex-direction: row | row-reverse | column | column-reverse;
  ```

- ### flex-wrap

  决定是否换行

  ```css
  flex-wrap: nowrap | wrap | wrap-reverse;
  ```

- ### flex-flow

  为flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap

- ###  justify-content

  定义了项目在主轴上的对齐方式。

  ```css
  justify-content: flex-start | flex-end | center | space-between | space-around;
  ```

- ###  align-items

  在交叉轴上如何对齐

  ```css
  align-items: flex-start | flex-end | center | baseline | stretch;
  ```

  `baseline`: 项目的第一行文字的基线对齐。

  `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度

- ### align-content

  定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用

  ```css
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
  ```

对盒子内部项目的属性如下

- ### order

  定义项目的排列顺序。数值越小，排列越靠前，默认为0。

  ```css
  order: <integer>;
  ```

- ### flex-grow

  定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大

  如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

- ### flex-shrink

  义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

- ### flex-basis

  定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

  ```css
  flex-basis: <length> | auto; /* default auto */
  ```

  它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间

- ###  flex

  `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

  ```css
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
  ```

- ###  align-self

  允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

  ```css
   align-self: auto | flex-start | flex-end | center | baseline | stretch;
  ```

## grid（网格布局）

`display: grid`指定一个容器采用网格布局。

display: inline-grid是在行内的一个网络布局

- #### 盒子属性

#### grid-template-columns 属性， grid-template-rows 属性

容器指定了网格布局以后，接着就要划分行和列。`grid-template-columns`属性定义每一列的列宽，`grid-template-rows`属性定义每一行的行高。

```css
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;//也可以使用%
  grid-template-rows: 100px 100px 100px;
}
```

- repeat
  grid-template-columns: repeat(3, 33.33%);
  grid-template-rows: repeat(3, 33.33%);
  有时候，重复写同样的值非常麻烦，尤其网格很多时。这时，可以使用repeat()函数，简化重复的值。上面的代码用repeat()改写如下。
  接受两个参数，第一个参数是重复的次数（上例是3），第二个参数是所要重复的值。

- auto-fill
  元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用auto-fill关键字表示自动填充

- fr

  为了方便表示比例关系，网格布局提供了`fr`关键字（fraction 的缩写，意为"片段"）。如果两列的宽度分别为`1fr`和`2fr`，就表示后者是前者的两倍。

- minmax()

  `minmax()`函数产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。
  grid-template-columns: 1fr 1fr minmax(100px, 1fr);
  上面代码中，`minmax(100px, 1fr)`表示列宽不小于`100px`，不大于`1fr`

- auto

  `auto`关键字表示由浏览器自己决定长度。

  ```css
  grid-template-columns: 100px auto 100px;
  ```

  上面代码中，第二列的宽度，基本上等于该列单元格的最大宽度，除非单元格内容设置了`min-width`，且这个值大于最大宽度。

- 网格线的名称

  `grid-template-columns`属性和`grid-template-rows`属性里面，还可以使用方括号，指定每一根网格线的名字，方便以后的引用。

  > ```css
  > .container {
  > display: grid;
  > grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
  > grid-template-rows: [r1] 100px [r2] 100px [r3] auto [r4];
  > }
  > ```

  上面代码指定网格布局为3行 x 3列，因此有4根垂直网格线和4根水平网格线。方括号里面依次是这八根线的名字。

  网格布局允许同一根线有多个名字，比如`[fifth-line row-5]`。

#### grid-row-gap 属性， grid-column-gap 属性， grid-gap 属性

`grid-row-gap`属性设置行与行的间隔（行间距），`grid-column-gap`属性设置列与列的间隔（列间距）。

```css
.container {
  grid-row-gap: 20px;
  grid-column-gap: 20px;
}
```

`grid-gap`属性是`grid-column-gap`和`grid-row-gap`的合并简写形式，语法如下。

```
grid-gap: <grid-row-gap> <grid-column-gap>;
```



#### grid-template-areas 

网格布局允许指定"区域"（area），一个区域由单个或多个单元格组成。`grid-template-areas`属性用于定义区域。

```css
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-template-areas: 'a b c'
                       'd e f'
                       'g h i';
}
```

上面代码先划分出9个单元格，然后将其定名为`a`到`i`的九个区域，分别对应这九个单元格。

如果某些区域不需要利用，则使用"点"（`.`）表示。

注意，区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为`区域名-start`，终止网格线自动命名为`区域名-end`。

比如，区域名为`header`，则起始位置的水平网格线和垂直网格线叫做`header-start`，终止位置的水平网格线和垂直网格线叫做`header-end`。

#### grid-auto-flow

划分网格以后，容器的子元素会按照顺序，自动放置在每一个网格。默认的放置顺序是"先行后列"，即先填满第一行，再开始放入第二行

默认值是`row`，即"先行后列"。也可以将它设成`column`，变成"先列后行"。

`grid-auto-flow`属性除了设置成`row`和`column`，还可以设成`row dense`和`column dense`。这两个值主要用于，某些项目指定位置以后，剩下的项目怎么自动放置

#### justify-items 属性， align-items 属性， place-items 属性

`justify-items`属性设置单元格内容的水平位置（左中右），`align-items`属性设置单元格内容的垂直位置（上中下）。

```css
.container {
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
```

`place-items`属性是`align-items`属性和`justify-items`属性的合并简写形式。

```css
place-items: <align-items> <justify-items>;
```

#### justify-content 属性， align-content 属性， place-content 属性

`justify-content`属性是整个内容区域在容器里面的水平位置（左中右），`align-content`属性是整个内容区域的垂直位置（上中下）。

```css
.container {
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
```

`place-content`属性是`align-content`属性和`justify-content`属性的合并简写形式。

#### grid-auto-columns 属性， grid-auto-rows 属性

`grid-auto-columns`属性和`grid-auto-rows`属性用来设置，浏览器自动创建的多余网格的列宽和行高。它们的写法与`grid-template-columns`和`grid-template-rows`完全相同。如果不指定这两个属性，浏览器完全根据单元格内容的大小，决定新增网格的列宽和行高。

#### grid-template 属性， grid 属性

`grid-template`属性是`grid-template-columns`、`grid-template-rows`和`grid-template-areas`这三个属性的合并简写形式。

`grid`属性是`grid-template-rows`、`grid-template-columns`、`grid-template-areas`、 `grid-auto-rows`、`grid-auto-columns`、`grid-auto-flow`这六个属性的合并简写形式。

- #### 项目属性

#### grid-column-start 属性， grid-column-end 属性， grid-row-start 属性， grid-row-end 属性

- `grid-column-start`属性：左边框所在的垂直网格线
- `grid-column-end`属性：右边框所在的垂直网格线
- `grid-row-start`属性：上边框所在的水平网格线
- `grid-row-end`属性：下边框所在的水平网格线

这四个属性的值还可以使用`span`关键字，表示"跨越"，即左右边框（上下边框）之间跨越多少个网格。

#### grid-column 属性， grid-row 属性

`grid-column`属性是`grid-column-start`和`grid-column-end`的合并简写形式，`grid-row`属性是`grid-row-start`属性和`grid-row-end`的合并简写形式。

####  grid-area

`grid-area`属性指定项目放在哪一个区域

`grid-area`属性还可用作`grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`的合并简写形式，直接指定项目的位置。

#### justify-self 属性， align-self 属性， place-self 属性

`justify-self`属性设置单元格内容的水平位置（左中右），跟`justify-items`属性的用法完全一致，但只作用于单个项目。

`align-self`属性设置单元格内容的垂直位置（上中下），跟`align-items`属性的用法完全一致，也是只作用于单个项目。

```css
.item {
  justify-self: start | end | center | stretch;
  align-self: start | end | center | stretch;
}
```









