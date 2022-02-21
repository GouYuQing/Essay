## HTML知识点

### 1.doctype的作用

主要作用是告诉浏览器的解析器使用哪种HTML规范或者XHTML规范来解析页面。

Doctype可声明三种DTD类型，分别表示严格版本、过渡版本以及基于框架的 HTML 文档。

在W3C标准出来之前，不同的浏览器对页面渲染有不同的标准，产生了一定的差异。这种渲染方式叫做混杂模式（兼容模式：向后兼容，模拟老式浏览器，防止浏览器无法兼容页面。）。

在W3C标准出来之后，浏览器对页面的渲染有了统一的标准，这种渲染方式叫做标准模式（严格模式：排版和JS 运作模式是 以该浏览器支持的最高标准运行。）

<!DOCTYPE>不存在或者形式不正确会导致HTML或XHTML文档以混杂模式呈现





### 2.tr and td

行标记tr及属性（**没有宽度属性**）

（1）行高度height（2）行的边框颜色bordercolor（3）行的背景颜色bgcolor

（4）行的对齐方式align（5）行的垂直对其方式valign

单元格td属性（**没有边框颜色**）

（1）高度height、宽度width（2）背景颜色bgcolor（3）背景图片background

（4）对齐方式align（5）垂直对其方式valign

（6）水平跨度colspan（7）垂直跨度rowspan（8）表头标记th与td类似

### 3.隐藏域的作用和用法

<input type="hidden" name="" value="">

隐藏域在页面中对于用户是不可见的，在表单中插入隐藏域的目的在于收集或发送信息，以利于被处理表单的程序所使用。浏览者单击发送按钮发送表单的时候，隐藏域的信息也被一起发送到服务器。 

1. 添加用户信息，提交表单时提交上来以确定用户身份，如sessionkey，等等．当然这些东西也能用cookie实现，但使用隐藏域就简单的多了．而且不会有浏览器不支持，用户禁用cookie的烦恼。 
2. 有些时候一个form里有多个提交按钮，怎样使程序能够分清楚到底用户是按那一个按钮提交上来的呢？我们就可以写一个隐藏域，然后在每一个按钮 处加上onclick="document.form.command.value="xx""然后我们接到数据后先检查command的值就会知道用户 是按的那个按钮提交上来的。 
3. 有时候一个网页中有多个form，我们知道多个form是不能同时提交的，但有时这些form确实相互作用，我们就可以在form中添加隐藏域来使它们联系起来。 
4. javascript不支持全局变量，但有时我们必须用全局变量，我们就可以把值先存在隐藏域里，它的值就不会丢失了。 
5. 还有个例子，比如按一个按钮弹出四个小窗口，当点击其中的一个小窗口时其他三个自动关闭．可是IE不支持小窗口相互调用，所以只有在父窗口写个隐藏域，当小窗口看到那个隐藏域的值是close时就自己关掉

### 4.iframe是什么？有什么缺点？

定义：iframe元素会创建包含另一个文档的内联框架

提示：可以将提示文字放在<iframe></iframe>之间，来提示某些不支持iframe的浏览器

缺点：

会阻塞主页面的onload事件

搜索引擎无法解读这种页面，不利于SEO（搜索引擎优化）

iframe和主页面共享连接池，而浏览器对相同区域有限制所以会影响性能。

### 5.锚t

```
<a name="tips">基本的注意事项 - 有用的提示</a>
```

然后，我们在同一个文档中创建指向该锚的链接：

```
<a href="#tips">有用的提示</a>
```

### 6.定位

| 描述     |                                                              |
| -------- | ------------------------------------------------------------ |
| absolute | **绝对定位**；脱离⽂档流的布局，遗留下来的空间由后⾯的元素填充。定位的起始位置为最近的⽗元素(postion不为static)，否则为Body⽂档本身。 |
| relative | **相对定位**；不脱离⽂档流的布局，只改变⾃身的位置，在⽂档流原先的位置遗留空⽩区域。定位的起始位置为此元素原先在⽂档流的位置。 |
| fixed    | **固定定位**；类似于absolute，但不随着滚动条的移动⽽改变位置。 |
| static   | **默认值；默认布局。** **忽略 top, bottom, left, right和z-index** |
| inherit  | **从父元素继承**该属性的值                                   |

以上的`absolute` 和`fixed` 可以使得元素脱离文档流。

浮动会脱离文档流

![image-20200330113918754](D:\code\jsWorkSpace\notes\Essay\HTML、CSS、JS学习笔记\HTML知识点.assets\image-20200330113918754.png)

![image-20200330113908638](D:\code\jsWorkSpace\notes\Essay\HTML、CSS、JS学习笔记\HTML知识点.assets\image-20200330113908638.png)

![image-20200330113928870](D:\code\jsWorkSpace\notes\Essay\HTML、CSS、JS学习笔记\HTML知识点.assets\image-20200330113928870.png)

clear:定段落的左侧或右侧不允许浮动的元素

```
div p h1 form ul 是块级元素
```

```
span a label input img em是内联元素
```

### 7.居中

单行内容垂直居中

- 设置行高和元素高度，且两者相等,设置over-flow:hidden

- text-algin:center

多行内容垂直居中

内部文字span包裹，设置为行内块 display:inline-block

外部div块固定宽，使用line-height 设置高度

### 8.用通俗的语言解释REST，以及RESTful

URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作

### 9.meta

所有浏览器支持

<meta> 元素可提供有关页面的元信息（meta-information），比如针对搜索引擎和更新频度的描述和关键词。
<meta> 标签位于head，不包含任何内容。<meta> 标签的属性定义了与文档相关联的名称/值对。

http-equiv 属性：`http`的文件头作用，它可以向浏览器传回一些有用的信息

比如：content-type:text/html。这将告诉浏览器准备接受一个 HTML 文档

name 属性：name 属性提供了名称/值对中的名称。告诉搜索引擎当前页面关键字（方便被搜索）

## HTML5知识点

### 1.网页制作的图片格式有哪些？

png、jpeg、gif、svg、webp

webp:

![C05E066739AA9ED4409F4F760A2C17E8](images/C05E066739AA9ED4409F4F760A2C17E8.jpg)

### 2.canvas常用方法

（1）getContext("2d") //得到画笔

（2）fillStyle颜色填充

（3）fillRect（）规定形状位置和尺寸

（4）moveTo(10,10)定义线条开始坐标

（5）lineTo(150,50)定义线条结束坐标

（6）stroke() ：绘制线条

（7）在canvas中绘制圆形, 我们将使用以下方法:

```css
arc(x,y,r,start,stop)
```

（8）font定义字体:ctx.font="30px Arial";

fillText(*text,x,y*)在 canvas 上绘制实心的文本

strokeText(*text,x,y*) - 在 canvas 上绘制空心的文本

  (9)drawImage(*image,x,y*):把一幅图像放置到画布上

### 3.contenteditable

在元素上设置以使内容可编辑的属性。 它适用于`div`，`p`，`ul`等元素

```html
<h2> Shoppping List(Content Editable) </h2>
 <ul class="content-editable" contenteditable="true">
     <li> 1. Milk </li>
     <li> 2. Bread </li>
     <li> 3. Honey </li>
</ul>
```

### 4.mark标签

```html
<p>
    <mark>hello</mark>        // 高亮展示
    world
</p>

mark {//可以更换高亮颜色
  background-color: green;
  color: #FFFFFF;
}
```

### 5..data-*属性

用于存储页面或者应用程序的自定义数据

```html
<h2> Know data attribute </h2>
 <div 
       class="data-attribute" 
       id="data-attr" 
       data-custom-attr="You are just Awesome!"> 
   I have a hidden secret!
  </div>

 <button onclick="reveal()">Reveal</button>
```

```js
function reveal() {
   let dataDiv = document.getElementById('data-attr');
    let value = dataDiv.dataset['customAttr'];
   document.getElementById('msg').innerHTML = `<mark>${value}</mark>`;
}
```

### 6.datalist

```html
<form action="" method="get">
    <label for="fruit">Choose your fruit from the list:</label>
    <input list="fruits" name="fruit" id="fruit">//input框控件的可选项
        <datalist id="fruits">
           <option value="Apple">
           <option value="Orange">
           <option value="Banana">
           <option value="Mango">
           <option value="Avacado">
        </datalist>
     <input type="submit">
 </form>  
```

### 7.color picker

```html
<input type="color" onchange="showColor(event)">
<p id="colorMe">Color Me!</p>
```

### 8.inputs

required、autofocus、pattern正则校验

```html
<input type="password" 
    name="password" 
    id="password" 
    placeholder="6-20 chars, at least 1 digit, 1 uppercase and one lowercase letter" 
    pattern="^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,20}$" autofocus required>
```

## HTML5.1知识点

### 1.响应式图片picture

```html
// srcset图像属性允许列出多个可用于替代的图片数据源，这些数据源的像素密度不同
// 如果用户设备的像素比是1，图片low-res将会被展示；如果是2，图片high-res将会被展示；如果是3或者更大，图片ultra-high-res将会被展示
<img src="images/low-res.jpg" srcset="
  images/low-res.jpg 1x, 
  images/high-res.jpg 2x, 
  images/ultra-high-res.jpg 3x"
>

// sizes根据用户屏幕尺寸来使用不同方式展示图片
//当视口宽度大于40em时，sizes属性把图片的宽度定义为视口宽度的50%；当视口（viewport）宽度小于或者等于40em时，把图片宽度定义为视口宽度的100%。
<img src="images/low-res.jpg" sizes="(max-width: 40em) 100vw, 50vw"
  srcset="images/low-res.jpg 600w, 
  images/high-res.jpg 1000w, 
  images/ultra-high-res.jpg 1400w"
>

//根据屏幕不同展示不同的图片,使用picture属性
<picture>
  <source media="(max-width: 20em)" srcset="
    images/small/low-res.jpg 1x,
    images/small/high-res.jpg 2x, 
    images/small/ultra-high-res.jpg 3x
  ">
  <source media="(max-width: 40em)" srcset="
    images/large/low-res.jpg 1x,
    images/large/high-res.jpg 2x, 
    images/large/ultra-high-res.jpg 3x
  ">
  <img src="images/large/low-res.jpg">
</picture>
```

### 2.细节（Details）和总结（Summary）元素

新的<details>和<summary>元素可以通过鼠标点击实现附加信息的展示和隐藏

```html
<details>
    <summary>HTML5.1</summary>
    <p>
       HTML5.1新特性
    </p>
    <ul>
        <li>hhh</li>
        <li>kkk</li>
        <li>oooo</li>
    </ul>
</details>
```

### 3.dialog对话框

```html
<dialog open>
	<p>
        hhhhhh
    </p>
</dialog>
```

open代表对话框可见，没有就会隐藏

```js
const modal = document.querySelector('dialog');
modal.showModal();//添加open属性打开对话框
modal.show();// 打开对话框，show打开对话框没有背景遮罩
modal.close();// 删除open属性

dialog::backdrop{// 伪元素设置背景遮罩样式
    background-color:rgba(0,0,0,0.4);
}
```

