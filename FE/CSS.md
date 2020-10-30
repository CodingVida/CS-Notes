[TOC]



### 1. 盒子模型

* 盒模型的内容：内容content、填充padding、边框border和外边距margin
* 盒子模型分两种：IE盒模型（border-box）和W3C标准盒模型（content-box)
* 两种盒模型的区别在于width和height的计算方式：
    * IE盒模型（border-box）的width和height包括了 padding 和 border
    * w3c标准盒模型（content-box）则不包含。
* 我们可以通过设置 box-sizing 属性来改变。

### 2. 选择器

* 分类

    * id
    * 类
    * 标签
    * 后代
    * 子选择器（`ul > li`)
    * 兄弟选择器（`li ~ a`)
    * 相邻兄弟选择器（`li + `)
    * 属性选择器（`a[rel="external"]` )
    * 伪类选择器（`a:hover`)
    * 伪元素选择器（`::after, ::before`)
    * 通配选择器（`*`)

* 优先级算法

    * 先查看规则权重（`!important`)，加了权重的优先级高；若权重相同，比较规则的特殊值。
    * 特殊值高的规则优先级高；特殊值相同则按照声明次序。
    * 后声明的优先级高。

* 特殊值

    * 分为4个等级

        |            **分类**            | **特殊值** |
        | :----------------------------: | :--------: |
        |            内联样式            |  x,0,0,0   |
        |            id选择器            |  0,x,0,0   |
        | 类选择器/属性选择器/伪类选择器 |  0,0,x,0   |
        |    标签选择器/ 伪元素选择器    |  0,0,0,x   |

        

    * 计算方法

        * 每个等级初始化为0，即 0,0,0,0
        * 同类选择器出现的次数叠加为同一等级个数
        * 不可进位
        * 等级间无关联
        * 判断从左到右
        * 通配选择器的特殊值为 0,0,0,0，但它高于继承样式（优先级最低)。

    * 示例

        ```css
        a{color: yellow;} 						/*特殊性值：0,0,0,1*/
        div a{color: green;} 					/*特殊性值：0,0,0,2*/
        .demo a{color: black;} 					/*特殊性值：0,0,1,1*/
        .demo input[type="text"]{color: blue;} 	 /*特殊性值：0,0,2,1*/
        .demo *[type="text"]{color: grey;} 		 /*特殊性值：0,0,2,0*/
        #demo a{color: orange;} 				/*特殊性值：0,1,0,1*/
        div#demo a{color: red;} 				/*特殊性值：0,1,0,2*/
        ```



### 3. 伪类和伪元素

​	css3 引入伪类和伪元素是**为了格式化文档树以外的信息**，比如第一个字母(first-letter)。

 * 伪类：单引号`:`。用于当**已有元素**处于某个状态时，添加对应的样式是根据用户行为而动态变化的。

     *	:first-child
     *	:visited

    ![](http://www.alloyteam.com/wp-content/uploads/2016/05/%E4%BC%AA%E7%B1%BB.png)

 * 伪元素：双引号`::`，为了向后兼容，大部分还可以使用单引号。用于**创建不在文档树中的元素**，并为其添加样式。

     *	::after/:afer
     *	::before/:before
     *	::first-letter、:first-letter；first-line
     *	::placeholder

    ![](http://www.alloyteam.com/wp-content/uploads/2016/05/%E4%BC%AA%E5%85%83%E7%B4%A0.png)

    

### 4. 属性继承

> 属性在定义中会给出是否具有继承性。但一个属性不是继承属性时，可以通过`inherit` 来指定从父元素继承同名属性。

可以继承的属性有：

	* 字体相关：font-size、font-weight等
	* 文本相关：color、text-align等
	* 表格布局属性
	* 列表属性：list-style等
	* 元素可见性：visibility

### 5.伪类LVHA的解释

> 一个声明次序问题。

a标签有四种状态：链接访问前、链接访问后、鼠标滑过、激活，分别对应四种伪类:link、:visited、:hover、:active；

* 当链接未访问过时：

    * 当鼠标滑过a链接时，满足:link和:hover两种状态，要改变a标签的颜色，就必须将:hover伪类在:link伪类后面声明；
    * 当鼠标点击激活a链接时，同时满足:link、:hover、:active三种状态，要显示a标签激活时的样式（:active），必须将:active声明放到:link和:hover之后。因此得出LVHA这个顺序。

* 当链接访问过时，情况基本同上，只不过需要将:link换成:visited。

> 这个顺序能不能变？可以，但也只有:link和:visited可以交换位置，因为一个链接要么访问过要么没访问过，不可能同时满足，也就不存在覆盖的问题。

### 6.居中

* width + margin: 0 auto；
* display: inline-block  + （parent）text-align: center;
* position: absolute; + top + left + margin(-)
* position: absolute; + top0 + bottom0 + left0 + right0 + margin(auto)
* position: absolute; + top + left + transform
* display: flex; + justify-content + align-items

> 后两种可以实现 **不定宽高** 垂直、水平居中。

### 7. dispaly的值

* block: 块类型。默认宽度为父元素宽度，可设置宽高，换行显示。
* none: 元素不显示，并从文档流中移除。
* inline: 行内元素类型。默认宽度为内容宽度，不可设置宽高，同行显示。
* inline-block默认宽度为内容宽度，可以设置宽高，同行显示。
* list-item: 像块类型元素一样显示，并添加样式列表标记。
* table: 此元素会作为块级表格来显示。
* inherit: 规定应该从父元素继承display属性的值。

### 8. 定位

* absolute
> 绝对定位，相对于值不为static的第一个**父元素的 paddingbox** 进行定位（即计算时需要加上padding）。

* fixed（老IE不支持）
> 固定定位，相对于浏览器窗口进行定位。

* relative
> 相对定位，相对于其元素本身所在正常位置进行定位。

* static

> 默认值。没有定位，元素出现在正常的流中（忽略top,bottom,left,right,z-index声明）。

### 9. css3新特性

* 新选择器：伪类伪元素
* 动画
* 变形：缩放、定位
* 圆角
* 阴影

### 10. FlexBox弹性盒子

1. 基本概念

    * 任何一个容器都可以指定弹性布局（行内元素也可以指定为flex布局）
    * 采用flex布局的元素称为 **Flex容器（flex container）**，其子元素自动称为容器成员，称为 **Flex项目（flex item）**

    * 设置flex布局后，子元素的 float、clear 和 vertical-align 属性将失效
    * 容器默认存在两根轴线：主轴（main axis）和 交叉轴（cross axis）。主轴默认在水平方向上。

2. 容器属性 x6

    * flex-direction：指定主轴方向，即项目排列方向。row、column 、**-reverse
    * flex-wrap：换行。nowrap、wrap、wrap-reverse（第一行在最下边）
    * flex-flow：flex-direction + flex-wrap 的简写。（默认row、nowrap）
    * justify-content：主轴上的对齐方式，默认flex-start。
        * flex-start、flex-end、center、space-between、space-around
        * space-between：与边框没有间距，彼此之间间距相等。
        * space-around：每个项目两侧的间距相等，所以与边框的间距是项目间的一半。
    * align-items：交叉轴上的对齐方式，默认stretch。
        * flex-start、flex-end、center、baseline、stretch。
        * baseline：与项目第一行文字的基线对齐。
        * stretch：如果项目未设置高度或为auto，将占满整个容器的高度。
    * align-content：多根轴线时与交叉轴的对齐方式（单根不起作用），默认为stretch。
        * flex-start、flex-end、center、space-between、space-around、stretch。
        * 场景比如：换行了，有多行项目。

3. 项目属性 x6

    * order：排列次序，数值越小越靠前，默认为0。

    * flex-basis：分配多余空间前项目占据的主轴空间（main size）
        * `flex-basis: <length> | auto; /* default auto `
        * 默认值为auto，项目本来的大小
    * flex-grow：放大比例
        * 默认为0，有剩余空间也不放大。
        * basis + free · grow / (grow1 + grow2 + ...)
    * flex-shrink：缩小比例
        * 默认为1，即空间不足时，该项目将缩小
        * 无负值
    * flex：flex-grow + flex-shrink + flex-basis
        * 默认值为：`0 1 auto`，（后两个属性可选）。
        * 快捷值为：auto ( 1 1 auto ) 和 none ( 0 0 auto )。
        * 建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。
    * align-self：允许单个项目与其他项目不同的与交叉轴的对齐方式。默认值为 auto，表示继承父元素的 align-items。

### 11. 纯css 三角形

> 相邻边框连接处的均分原理。

比如，实现右三角，宽高10px：

```css
.triangle {
    width: 0;
    height: 0;
    border-width: 5px 0 5px 10px;
    border-style: solid;
    border-color: transparent transparent transparent red;
}
```



### 12. 品字

上边width 100%，下边 50%。



### 13. 多列等高

* padding-bottom 和 margin-bottom 正负相抵法
    * `padding-bottom: 99999px; margin-bottom: -9999px;`。
    * 父容器overflow：hidden，其高低不设定，将等于最高一列的高度。
    
* flex布局：align-items的 stretch。

* table-cell所有单元格等高。

    

### 14. li 与 li 之间看不见的空白符

 * 原因

    > 浏览器会把 inline元素间的空白字符（空格、换行、Tab等）渲染成一个空格。而为了代码美观，我们通常是一个<li>放在一行，这导致<li>换行后产生换行字符，它会被渲染成一个空格，占用一个字符的宽度。

 * 解决方法

    	* li 设置 float，但有些容器不能设置浮动，比如左右切换的焦点图
     
     * 放在同一行，但代码不美观
     
     * 父容器 font-size: 0;，但需要额外设置其他的字体大小，且Safari中无效。
     
     * 父容器 letter-spacing: -8px，同是需要额外设定...
     
         

### 15. 包含块（containnig block）

* 一个元素的位置和尺寸经常受其包含块的影响，在赋予百分比值时，这些值的计算要通过包含块来计算。

* 确定包含块：确定一个元素的包含块**完全依赖于**这个元素的 position属性。

    * position为 static/relative/sticky时，包含块由它的最近祖先块元素（比如 block、inline-block或list-item元素）的**内容区**边缘组成（content-box）。
    * position为absolute时，由最近的position不是static（也就是为relative、absolute、fixed或sticky）的祖先元素的 **内边距**边缘（padding-box）组成。
    * position为fix时，由viewport或page area组成（也可以称之为初始包含块）。

    > 根元素<html>所在的包含块是一个被称为 初始包含块 的矩形。

* 计算百分值

    * height、top、bottom 根据 包含块的 height来计算（parent height 没设定则为auto）
    * width、left、right、padding 和 margin 根据 包含块 的 width 来计算

### 16. visibility的属性值 collapse

> 可见与否，占用空间与否。

### 17. width: auto; 和 width: 100%

* width: 100%; 会使得元素的宽度等于父元素的 content-box的宽度。
* width: auto; 会使得元素撑满整个父元素的content-box，margin、border、padding等会自动分配。

### 18. 绝对定位和非绝对定位元素的百分比计算

* 绝对定位：相对于临近的position不为static的祖先元素的 padding-box 计算。
* 非：父元素的 content-box计算。

### 19. 图片base64编码

* 定义：base64编码是一种图片处理格式，通过特定算法将图片编码成一长串字符串，在页面上显示的时候，可代替图片上的url属性。

    > 早期传输协议只能传输可打印字符；
    >
    > base64 只支持64个可打印字符`A-Za-z0-9+/`；
    >
    > 每三个8bit的字节转换为四个6bit的字节（其高位填充00），理论上转换后文件增大 **1 / 3**。

* 优点：

    * 减少一个http请求。

* 缺点：

    * 根据算法，编码后文件增大1 / 3，如果编码到html、css中，会增大文件体积，影响加载速度和渲染时间。（网站的小图标可以引入）。
    * 不能缓存，除非缓存整个文件
    * （不用理会）ie8以前的浏览器不支持。

### 20. display、position和float的相互关系

* display
    * none: position 和 float 不起作用。
    * 否则，position
        * 若为 absolute 或 fixed：float无效、display转换
        * 否则：float
            * none：
                * 不是根元素：display为原来的指定值
                * 根元素：display转换值为 block或table
            * 否则：display转换

>  总的来说，可以把它看作是一个类似**优先级**的机制，"position:absolute"和"position:fixed"优先级最高，有它存在的时候，浮动不起作用，'display'的值也需要调整；其次，元素的'float'特性的值不是"none"的时候或者它是根元素的时候，调整'display'的值；最后，非根元素，并且非浮动元素，并且非绝对定位的元素，'display'特性值同设置值。

### 21. BFC

* BFC，block formatting context，块级格式化上下文。一个元素形成BFC之后，就相当于一个隔离区域，布局不影响到外部元素，外部元素也不会影响到bfc中的元素。
* 触发条件
    * 根元素或包含根元素的元素
    * 浮动元素，float不为none
    * 绝对定位元素，position为 absolute 或 fixed
    * overflow值不为visible的块元素
    * display为 `inline-block` | flow-root | table-cell | table-caption的元素
* 约束规则
    * BFC内的盒子垂直方向上排列（可以看做BFC中有一个常规流）；
    * 外边距折叠（margin collapse）只发生在同一BFC的块元素之间；
    * 水平方向上，盒子的左边缘与BFC的左边缘对齐，浮动元素也是如此；
    * BFC区域不会与外部浮动元素重叠，而是会依次排列；
    * 计算BFC的高度时，内部浮动元素要参与计算。
* 应用：
    * 垂直外边距重叠问题（比如父子块元素margin-top重叠）
    * 清除浮动，比如父元素设置无副作用的 `display：flow-root`创建一个根元素流动布局上下文，形成BFC；
    * 自适应两列布局（左侧 `float: left`, 右侧 `overflow：hidden`)

### 22. margin重叠

* margin重叠是指，在**垂直方向**上，相邻元素的margin发生重叠的情况。
* 一般分为四种情况：
    * 相邻兄弟元素的 `margin-bottom` 和 `margin-top` 重叠。此时使得其中一个称为BFC可以解决问题。
    * 父元素的 `margin-top` 和 子元素的 `margin-top` 重叠。它们重叠也是因为是相邻的，所以解决的思路是分隔它们。 
        * 父元素设置 border-top 或者 padding-top
        * 父元素设置成BFC
    * 高度为 `auto` 的父元素的 `margin-bottom` 和 子元素的 `margin-bottom` 重叠。它们重叠也是一是因为是相邻的，二是因为父级元素的高度不固定。所以解决的思路：
        * 给父级元素设置一个高度：`height`、`min/max-height` 都可以。
        * 父元素设置 `border-top` 或者 `padding-top`
        * 父元素设置成BFC
    * 没有内容的元素 `margin-top` 和 `margin-bottom` 重叠，设置border、padding或者高度可以解决。

### 23. IFC

* IFC，行级格式化上下文
* 布局规则：
    * 内部的内联盒子水平方向一个接一个放置
    * 一行不够自动切换到下一行
    * IFC的高度由内部最高的内联盒子决定

### 24. 清除浮动

> 高度坍塌。

### 25. clear属性清除浮动的原理

> “元素盒子的边不能和前面的浮动元素相邻”。
>
> clear属性只有块级元素有效。

### 26. 媒体查询



### 27. css优化，提高性能

* 加载性能
    * 文件压缩
    * 减少`@import`的使用，因其在页面加载完之后进行加载
* 选择器性能
    * 避免使用通配规则 `*{}`
    * 减少后代选择器的使用，尽量降低选择器深度
    * 了解哪些属性时可继承的，减少重复声明。
* 渲染性能
    * 慎重使用浮动、定位等高耗能属性
    * 减少页面重排、重绘
    * 属性值为0时，不加单位
    * css雪碧图，可以减少请求次数，但会使得图片本身变大，权衡使用。

### 28. 样式抽离模块的思路

[css规范-分类方法]:http://nec.netease.com/standard/css-sort.html



### 29. css3 all属性

* 是除了 unicode-bidi 和 direction(inherit/ltr/rtl) 这两个控制文本方向属性之外的所有属性的缩写。
* 三个属性值：
    * initial：初始值
    * inherit：继承父元素的属性值
    * unset：如果是继承元素，使用继承制；否则使用初始值。

### 30. absolute的包含块计算方式与正常流的不同

* 内联元素也可以作为它的包含块
* 包含块所在的元素不是父级块级元素，而是最近的position不为static的祖先元素。
* 边界是`padding-box`

### 31. 元素的竖向百分比

* height 相对于 包含块的 高度
* margin、padding 则是相对于宽度

### 32. 响应式设计

* 定义：响应式网站设计是一个网站能够兼容多个终端，而不是为每一个终端做一个特定的版本。
* 基本原理：通过媒体查询检测不同的设备屏幕尺寸做处理; 页面头部必须有meta声明的viewport。

### 33.  让Chrome支持小于12px的文字

* transform
* 图片

### 34. 设备像素、css像素、设备独立像素，dpr和 ppi

* 设备像素：即物理像素，一般手机的分辨率就是设备像素。
* css像素，与设备独立像素是等价的，是web编程中的概念，是一个相对的单位。
* dpr = 设备像素 / 设备独立像素。一般pc屏幕的dpr为1；iphone 6 的dpr 为2.
* ppi，每英尺的物理像素密度。屏幕大小一致的情况下，ppi越大，屏幕的分辨率越大。

### 35. viewport

> 移动端需要理解三个 `viewport` 的概念。

* `layout viewport`：布局窗口。

    * 移动端浏览器提供 布局窗口，用来 **布局展示** 页面，即页面实际呈现在此。
    * 一般 layout viewport 的大小为 **980px**，是一个实际存在的窗口。
    * 可以通过 `document.documentElemtn.clientWidth` 获取。

* `visual viewport`：视觉窗口。

    * 移动设备上的可见区域，一般为设备的分辨率。
    * 可以通过 `window.innerWidth` 获取。

* `ideal viewport`：理想窗口（是一个抽象概念）。

    * 由于layout viewport的大小要比 visual viewport 大，因此需要通过拖动缩放才能看到完整的页面。

    * ideal viewport 下，用户不用拖动缩放即可看到完整的页面，且在不同分辨率下显示的大小相同。

    * 实际上是通过meta标签使得 layout viewport 的宽度等于设备宽度：

        `<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">`

### 36. 手写动画，最小时间间隔为多少，为什么

多数显示器的默认刷新频率是 `60Hz`，即1s刷新60次，所以理论上最小时间间隔为 1000 / 60 = 16.7 ms。

### 37. 有一个高度自适应的 div，里面有两个 div，一个高度 100px，希望另一个填满剩下的高度

* flex：
    * flex-direction：column；
    * flex： 1；
* 父容器relative，需要填满的容器：`position: absolute; top: 100px; bottom: 0; left: 0; right: 0;`

### 38. 图片知识

#### 1 前置知识

* 有损 vs 无损
    * 有损压缩：在压缩图片的过程中，不可逆地损失一部分图片信息。常见的手段是合并临近像素点。
    * 无损压缩：在压缩图片的过程中，图片的质量没有任何损耗。
* 索引色 vs 直接色
    * 索引色（index color）：用一个字节（256种颜色）的数字来表示（索引）一种颜色。在存储图片时，存储数字的集合和数字到图片颜色的映射。
    * 直接色（direct color）：使用四个数字表示一种颜色，分别表示红绿蓝和透明度。四个维度分别支持256中变化，理论上最多可以支持2的32方种颜色。
* 点阵图 vs 矢量图
    * 点阵图，也叫位图。
        * 构成点阵图的最小单位是像素，每个像素有自己的颜色信息。
        * 缩放会失真。
    * 矢量图，也叫向量图。
        * 不记录每一点的信息，而是记录了形状及颜色的算法。
        * 不失真。

#### 2.图片格式

* BMP格式，它是无损压缩的，支持索引色和直接色的点阵图。由于它基本上没有进行压缩，因此它的文件体积一般比
较大。
* GIF格式，它是无损压缩的使用索引色的点阵图。由于使用了LZW压缩方法，因此文件的体积很小。并且GIF还
支持动画和透明度。但因为它使用的是索引色，所以它适用于一些对颜色要求不高且需要文件体积小的场景。
* JPEG格式，它是有损压缩的使用直接色的点阵图。由于使用了直接色，色彩较为丰富，一般适用于来存储照片。但
由于使用的是直接色，可能文件的体积相对于GIF格式来说更大。
* PNG-8格式，它是无损压缩的使用索引色的点阵图。它是GIF的一种很好的替代格式，它也支持透明度的调整，并
且文件的体积相对于GIF格式更小。一般来说如果不是需要动画的情况，我们都可以使用PNG-8格式代替GIF格式。
* PNG-24格式，它是无损压缩的使用直接色的点阵图。PNG-24的优点是它使用了压缩算法，所以它的体积比BMP
格式的文件要小得多，但是相对于其他的几种格式，还是要大一些。
* svg格式，它是矢量图，它记录的图片的绘制方式，因此对矢量图进行放大和缩小不会产生锯齿和失真。它一般
适合于用来制作一些网站logo或者图标之类的图片。
* webp格式，它是支持有损和无损两种压缩方式的使用直接色的点阵图。使用webp格式的最大的优点是，在相
同质量的文件下，它拥有更小的文件体积。因此它非常适合于网络图片的传输，因为图片体积的减少，意味着请求时间的减小，
这样会提高用户的体验。这是谷歌开发的一种新的图片格式，目前在兼容性上还不是太好。



### 39. 判断浏览器是否支持webp格式图片

* 宽高判断法：创建image对象，将其src设置为webp格式的图片，然后尝试在onload中获取宽高信息。
* cavas判断法：创建canvas对象，通过type参数为 `png/webp` 的toDataURL方法，判读返回值中是否有 `image/webp` 字符串。

### 40. cookie隔离



### 41. css 预处理器 和 后处理器

* css预处理器定义了一种新的编程语言，它将css视为目标生成文件。它的层级、变量、mixins和函数等特性提高了开发效率。
* 后处理器对css进行处理，比如添加浏览器私有前缀。

### 42. css sprites

* 将一个页面上涉及到的图片合并到一张大图中，利用 `background-image`、`background-position` 进行背景定位。
* 优点：
    * 减少http请求
    * 增加图片信息重复度，提高压缩比，减小文件大小
* 缺点：
    * 图片合并麻烦
    * 维护麻烦

### 43. 画一条 0.5px 的线

* tranform: scaleY(0.5)

    ```css
    .scale-half {
    	height: 1px;
        transform: scaleY(0.5);
        transform-origin: 50% 100%; // 默认情况下值为 50% 50% 0，以元素中心为缩放点，Chrome下会模糊。
    }
    ```

    

* svg

    ```css
    <svg xmlns="" with="100%" height="1px">
    	<line x1="0" y1="0" x2="100%" y2="0" stroke="#000" />
    </svg>
    ```

### 44. transition 和 animation 的区别

* transition 关注的 css 的属性值
* animation 是作用于元素本身而不是属性值，可以使用**关键帧**实现更自由的动画效果。



### 45. min-width 、max-width 的覆盖规则

> min-height、max-height也是如此。

* `max-width` 会 覆盖 `width`，即便 `width` 是内联样式或者设置了 `!important`。
* `min-width` 会覆盖 `max-width`

### 46. 替换元素

* 通过修改某个属性值的内容就可以被替换的元素称为 **替换元素**。
* 计算规则：
    * 如果有css尺寸，使用css尺寸；
    * 没有css尺寸，有html尺寸，使用html尺寸（即width、height、cols等属性）
    * 没有css尺寸和html尺寸，则使用固有尺寸。
    * 如果上述均不符合，一般表现为宽300px、高150px
    * 内联替换元素和块级替换元素均是。

### 47. 层叠上下文

* 层叠上下文，stacking context，是html中的一个三维概念。如果一个元素含有层叠上下文，那么可以理解为它在z轴上“高人一等”。
* 特性：
    * 层叠水平比普通元素高
    * 阻断元素的混合模式
    * 可以嵌套，内部层叠上下文及其子元素均受制于外部的层叠上下文
    * 兄弟上下文互相独立，自称体系
* 创建：
    * 页面根元素
    * position为relative、absolute和fixed
    * 使用css3属性，比如元素的opacity值不是1
* 层叠水平：stacking level
	* 决定了同一个层叠上下文中元素在z轴上的显示顺序
	* 所有元素都有（包括普通元素）
* 层叠顺序：stacking order
    * 表示元素发生层叠时的垂直显示顺序。
    * ![](https://camo.githubusercontent.com/6efc5f16fac4d2bd779f16596b97f27962615028/68747470733a2f2f636176737a686f75796f752d313235343039333639372e636f732e61702d63686f6e6771696e672e6d7971636c6f75642e636f6d2f6e6f74652d31352e706e67)
* 层叠准则：
    * 谁大谁上
    * 后来居上



### 48.  word-spacing

letter-spacing作用于所有字符，但word-spacing仅作用于空格字符。换句话说，word-spacing的作用就是增加空格的间隙
宽度。

