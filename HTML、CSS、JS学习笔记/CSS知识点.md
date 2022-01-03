# CSS知识点

### 1.经典垂直居中

```css
/* 方法一 flex布局 */
body{
	display: flex;
	justify-content: center;
	align-items: center;
	height: 100vh;
} 
			
/* 方法二 绝对定位+transform: 不需要知道自身尺寸*/
 .one{
	width: 100px;
	height: 100px;
	border: 1px solid red;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%,-50%);				
}
	/* //方法三 子绝父相:必须知道自身尺寸*/
 .one{
	width: 100px;
	height: 100px;
	border: 1px solid red;
	position: absolute;
	top: 50%;
	left: 50%;
	margin-top: -50px;				
}
	
	/* //方法四 子绝父相 类似方法二*/
	.one{
		width: 100px;
		height: 100px;
		border: 1px solid red;
		background-color: red;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translateY(-50%);				
	}
	/* 方法五 margin：auto*/
	.one{
		width: 100px;
		height: 100px;
		border: 1px solid red;
		background-color: red;
		position: absolute;
		top:0;
		buttom:0;
		left:0;
		right:0;
		margin:auto;
	}
	/*方法六*/
	body{
		display: table;
	}
	.one{
		width: 100px;
		height: 100px;
		border: 1px solid red;
		display: table-cell;
		vertical-align: center;/*vertical-align属性只对拥有valign特性的html元素起作用，例如表格元素中的<td><th>等等，而像<div><span>这样的元素是不行的。*/
} 
```



### 2.::first-letter和其他选择器

![image-20200308220329449](D:\code\jsWorkSpace\notes\Essay\HTML、CSS、JS学习笔记\images\image-20200308220329449-1639405111462.png)

![image-20200308220355153](./images/image-20200308220355153.png)

**为什么第一个字不会被class选择器和id选择器的颜色覆盖？**

**原因：**伪元素能在文档中插入假想元素从而的到某种效果,相当于在“我”两边加上了``标签，而``对于父级是继承关系，继承是没有优先级，所以被伪元素选择器覆盖了，显示成这个样子，有种降维打击的感觉。

**颜色等权重永远最高**
这是非常容易犯的一个错误，也是CSS世界10大不理解问题之一。

```html
p:first-letter {
    color: red;
}
p > span {
    color: blue !important;
}
<p><span>第一个</span>字符看看会不会变红？</p>
```

请问“第”这个字符的颜色是什么？

基本上，超过95%的前端小伙伴会认为是`blue`，因为大家都是从CSS选择器权重的角度去考虑的，本身是没问题，但是却忽略了很重要的一个点，`::first-letter`伪元素其实是作为子元素存在的，或者说应当看出是子元素，于是，就很好理解了，对于类似`color`这样的继承属性，子元素的CSS设置一定比父元素的级别要高，哪怕父级使用了重量级的`!important`，因为子元素会先继承，然后再应用自身设置，所以，上面CSS和HTML的最终结果是，第一个字符“第”字的颜色是`red`红色！

这就是`::first-letter`伪元素的另外一个重要特性，颜色等权重永远最高。

### 3.块级元素，行内元素，行内块元素对比

| 元素模式 | 元素排列     | 设置样式     | 默认宽度       | 包含                     |
| -------- | ------------ | ------------ | -------------- | ------------------------ |
| 块级元素 | 一行一个     | 可以设置宽高 | 容器的100%     | 容器级可以包含任何的标签 |
| 行内元素 | 一行可以多个 | 不可设置宽高 | 本身内容的宽度 | 文本或者其他行内元素     |
| 行内块   | 一行可以多个 | 可以设置宽高 | 本身内容的宽度 |                          |

### 4.CSS清除浮动

**举个例子**

```css
.news {
  	background-color: gray;
 	border: solid 1px black;
 }

.news img {
  	float: left;
 }

.news p {
  float: right;
 }

<div class="news">
<img src="news-pic.jpg" />
<p>some text</p>
</div>
```

![image-20211213224314583](./images/image-20211213224314583.png)

上述例子图片和文本都没有在div里面了，因为图片和文本都进行了浮动，打乱了原本的结构，造成了父元素高度塌陷，

浮动还会造成以至于父元素很多像背景颜色，边框等都不能正确的显示出来，同级子元素的位置不对等问题

#### （1）使用带clear的空元素

```css
.news {
 	background-color: gray;
 	border: solid 1px black;
}

.news img {
 	float: left;
}

.news p {
  	float: right;
 }

.clear { /*给新的空元素加clear:both*/
  	clear: both;
 }

<div class="news">
<img src="news-pic.jpg" />
<p>some text</p>
<div class="clear"></div> //新加空元素
</div>
```

#### （2）给父容器加overflow：hidden属性

```css
.news {
  background-color: gray;
  border: solid 1px black;
  overflow: hidden; /*给浮动元素的容器添加overflow:hidden;或overflow:auto;可以清除浮动*/
  *zoom: 1; /*IE6 中还需要触发 hasLayout ，例如为父元素设置容器宽高或设置 zoom:1*/
  }

.news img {
  float: left;
  }

.news p {
  float: right;
  }

<div class="news">
<img src="news-pic.jpg" />
<p>some text</p>
</div>
```

#### （3）给父容器加浮动，全部浮动，但是会影响布局

#### （4）:after伪元素

```css
.news img {
  float: left;
  }

.news p {
  float: right;
  }

.clearfix:after{ /*新增伪元素*/
  content: "020"; 
  display: block; 
  height: 0; 
  clear: both; 
  visibility: hidden;  
  }

.clearfix {
  /* 触发 hasLayout IE6 */ 
  zoom: 1; 
  }

<div class="news clearfix">// 给父元素加个类名
<img src="news-pic.jpg" />
<p>some text</p>
</div>
```

#### （5）给父元素设置高度

### 5.渐进增强与正常降级之间的区别

```css
/*渐进增强（progressive enhancement）*/
.transition{
  -webkit-transition: all .5s;
     -moz-transition: all .5s;
       -o-transition: all .5s;
          transition: all .5s;  
}
```

```css
/*优雅降级*/
.transition{ 
　　     transition: all .5s;
　　  -o-transition: all .5s;
  　-moz-transition: all .5s;
 -webkit-transition: all .5s;
}
```

渐进增强（progressive enhancement）：**先对低版本浏览器进行操作，再对高级浏览器进行效果展示**

优雅降级（graceful degradation）：**一开始就构建完整的功能，然后再针对低版本浏览器进行兼容**。

区别：优雅降级是从复杂的现状开始，并试图减少用户体验的供给，而渐进增强则是从一个非常基础的、能够起作用的版本开始，并不断扩充，以适应未来环境的需要。

### 6.BFC（块级格式化上下文）

如何创建BFC
1、float的值不是none。
2、position的值不是static或者relative。
3、display的值是inline-block、table-cell、flex、table-caption或者inline-flex
4、overflow的值不是visible

### 7.vh vw rem px

vh:相当于视口的高度，视口被均分为100单位的vh

```js
font-size:8vh;//如果视口的高度是200mm，那么上述代码中h1元素的字号将为16mm，即(8x200)/100
```

vm:相对于视口的宽度，视口被均分为100单位的vw

```js
font-size:8vw;//如果视口的高度是200mm，那么上述代码中h1元素的字号将为16mm，即(8x200)/100
```

rem：相对长度单位，相对于根元素(即html元素)[font-size](https://www.html.cn/book/css/properties/font/font-size.htm)计算值的倍数

```css
html{
	font-size:12px;
}
h1{
	font-size:2rem;//表示24px
}
```

px：像素

### 7.CSS可继承元素

颜色，文字，字体间距行高对齐方式，和列表的样式可以继承。 

所有元素可继承：visibility和cursor。 

内联元素可继承：letter-spacing、word-spacing、white-space、line-height、color、font、font-family、font-size、font-style、font-variant、font-weight、text-decoration、text-transform、direction。 

终端块状元素可继承：text-indent和text-align。 

列表元素可继承：list-style、list-style-type、list-style-position、list-style-image。

### 8.行内(inline)元素 设置margin-top和margin-bottom 是否起作用?

不起作用

### 9.对内联元素设置padding-top和padding-bottom是否会增加它的高度?

不会

### 10.设置p的font-size:10rem，当用户重置或拖曳浏览器窗口时，文本大小是否会也随着变化?

rem是以html根元素中font-size的大小为基准的相对度量单位，文本的大小不会随着窗口的大小改变而改变。

### 11.CSS加载会造成阻塞吗？

从浏览器的渲染过程来看

![image-20220102214907554](images/image-20220102214907554.png)

1. css加载不会阻塞DOM树的解析【DOM解析和CSS解析是两个并行的过程】
2. css加载会阻塞DOM树的渲染【Render树在解析之后】
3. css加载会阻塞后面js语句的执行【js可能会操作DOM和CSS样式，所以应该维持顺序，先HTML，CSS再js】

为了避免让用户看到长时间的白屏时间，我们应该尽可能的提高css加载速度

方法：

1. 使用CDN(因为CDN会根据你的网络状况，替你挑选最近的一个具有缓存内容的节点为你提供资源，因此可以减少加载时间)
2. 对css进行压缩(可以用很多打包工具，比如webpack,gulp等，也可以通过开启gzip压缩)
3. 合理的使用缓存(设置cache-control,expires,以及E-tag都是不错的，不过要注意一个问题，就是文件更新后，你要避免缓存而带来的影响。其中一个解决防范是在文件名字后面加一个版本号)
4. 减少http请求数，将多个css文件合并，或者是干脆直接写成内联样式(内联样式的一个缺点就是不能缓存)

### 12.white-space,word-break,word-wrap(overflow-wrap)

`white-space:normal|nowrap|pre|pre-wrap|pre-line;`**控制空白字符显示**

```css
white-space:nowrap;/*空格被合并成一个空格，永不换行*/
white-space:pre/*保留空格，但是自动换行没有了*/
white-space:pre-wrap;/*保留空格和换行*/
white-space:pre-line;/*空格被合并了，但是换行符可以发挥作用*/
```

| 能否发挥作用 | 换行符   | 空格   | 自动换行     | &nbsp |
| ------------ | -------- | ------ | ------------ | ----- |
| normal       | 不能换行 | 被合并 | 可以自动换行 | √     |
| nowrap       | 不能换行 | 被合并 | 不能自动换行 | √     |
| pre          | 可以换行 | 保留   | 不能自动换行 | √     |
| pre-wrap     | 可以换行 | 保留   | 可以自动换行 | √     |
| pre-line     | 可以换行 | 被合并 | 可以自动换行 | √     |

`word-break:normal|break-all|keep-all`，**控制单词如何被拆分换行的**

```css
word-break:keep-all;/*单词不拆分换行，只有空格会拆分换行*/
word-break:break-all;/*所有单词碰到边界一律拆分换行,可能一个单词会被拆在两行显示，无论长短*/
```

`word-wrap（overflow-wrap）:normal|break-word`**超过一行的单词是否被拆分换行的,作为word-break的互补**

```css
word-wrap:break-word;/*当一个单词一整行都显示不下的时候，才显示换行*/
```

其实word-break也有break-word属性值，但是只有chrome和safari部分浏览器支持



# CSS3知识点

### 1.画一条0.5px的线

（1）采用meta viewport的方式

```xml
<meta name="viewport" content="width=device-width, initial-scale=0.5, minimum-scale=0.5, maximum-scale=0.5"/>
```

这样子就能缩放到原来的0.5倍，如果是1px那么就会变成0.5px

要记得viewport只针对于移动端，只在移动端上才能看到效果

（2）采用transform: scale()的方式，在垂直方向缩小0.5

```css
transform: scale(0.5);
transform-origin: 50% 100%; // 要指定origin值, 要不然会模糊
```

### 2.哪些是CSS3新出来的

（1）边框：border-radius、border-shadow、border-image

（2）渐变：Gradients、

（3）2D、3D：transform

（4）多媒体查询：

```xml
1.@media screen and (max-width: 699px) and (min-width: 520px)
2.<link rel="stylesheet" media="mediatype and|not|only (expressions)" href="print.css">
```

（5）CSS3弹性盒子

```css
.flex-container {
    display: -webkit-flex;
    display: flex;
    width: 400px;
    height: 250px;
    background-color: lightgrey;
}
 
.flex-item {
    background-color: cornflowerblue;
    width: 100px;
    height: 100px;
    margin: 10px;
}
<div class="flex-container">
  <div class="flex-item">flex item 1</div>
  <div class="flex-item">flex item 2</div>
  <div class="flex-item">flex item 3</div> 
</div>
```

### 2.Flex布局

https://juejin.im/post/6844903574929932301#heading-64

### 3.**css三种动画**

（1）`transition`实现渐变动画

```css
transition: all 2s ease-in 500ms;
/*property:填写需要变化的css属性
duration:完成过渡效果需要的时间单位(s或者ms)
timing-function:完成效果的速度曲线（linear,ease,ease-in,ease-out等等）
delay:延迟*/
```

（2）transform`转变动画`:2D和3D

```css
transform:scale(0.8, 1.5) rotate(35deg) skew(5deg) translate(15px, 25px);
/*rotate(angle)，2D 旋转，*/
rotate(x,y,z,angle)，3D旋转
/*缩放(scale) 分为2D和3D x,y,z是收缩比例
倾斜(skew)：主要用于对元素的样式倾斜
移动(translate)：主要用于将元素移动*/
```

（3）`animation`实现自定义动画，

```css
&：hover{
		animation-name: bounce;
         animation-duration: 3s;
         animation-iteration-count: infinite;
     }
 }
@keyframes bounce{
     0% {
         top: 0px;
    }
     50% {
         top: 249px;
         width: 130px;
         height: 70px;
    }
     100% {
         top: 0px;
    }
}
/*name：需要绑定到选择器的keyframe名称。
duration：完成该动画需要花费的时间，秒或毫秒。
timing-function：跟transition-linear一样。
delay：设置动画在开始之前的延迟。
iteration-count：设置动画执行的次数，infinite为无限次循环。
direction：是否轮询反向播放动画。normal，默认值，动画应该正常播放；alternate，动画应该轮流反向播放。*/
```

# CSS小技巧

### 1.css实现顶部进度条，随着页面滚动进度实现长短变化

![image-20220102220750113](images/image-20220102220750113.png)

```css
/*使用线性渐变来实现功能*/
/*整个body添加从左下角到右上角的线性渐变*/
body {
    background-image: linear-gradient(to right top, #ffcc00 50%, #eee 50%);
    background-repeat: no-repeat;
}

```

![image-20220102221006033](images/image-20220102221006033.png)

```css
/*运用一个伪元素，把多出来的部分遮住*/
body::after {
    content: "";
    position: fixed;
    top: 5px;
    left: 0;
    bottom: 0;
    right: 0;
    background: #fff;
    z-index: -1;
}
/*最后调整渐变高度*/
body {
    background-image: linear-gradient(to right top, #ffcc00 50%, #eee 50%);
    background-size: 100% calc(100% - 100vh + 5px);/*让滑动条和右上角贴合*/
    background-repeat: no-repeat;
}

```

[查看文章](https://juejin.cn/post/6844903758074216462)