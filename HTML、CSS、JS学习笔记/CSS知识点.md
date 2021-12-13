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

# CSS3知识点

