# CSS

**css的世界诞生就是为图文信息展示服务的**

## 块级元素

**block**

实际由外在的“块级盒子”和内在的“块级容器盒子”组成，

**inline-block** 

外在的“内联盒子”和内在的“块级容器盒子”组成，值为 inline 的元素则内外均是“内联盒子”

**width-height**作用在容器盒子

在页面丢一个div进去他就会像水一样铺满容器

## 



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