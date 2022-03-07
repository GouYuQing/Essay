### 1.map会改变原数组吗？

map()为操作数组的一种方法，官方文档显示：

1. map() 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。
2. map() 方法按照原始数组元素顺序依次处理元素。
3. **注意：** map() 不会对空数组进行检测。
4. **注意：** map() 不会改变原始数组。

#### （1）当数组为基础类型的时候，原数组不变

```js
    let array=[1,2,3,4,5]
    let newArray=array.map((item) => item*2)
    console.log(array); // [1,2,3,4,5]
	console.log(newArray) //[2,4,6,8,10]
```

#### （2）当数组是引用类型的时候原数组改变

```js
 let array = [{ name: 'Anna', age: 16 }, { name: 'James', age: 18 }]
    let newArray=array.map((item) => {
      item.like='eat';
      return item;
    })
    console.log(array); // [{ name: 'Anna', age: 16,like: "eat"},{ name: 'James', age: 18,like: "eat"}]
    console.log(newArray);//[{ name: 'Anna', age: 16,like: "eat"},{ name: 'James', age: 18,like: "eat"}]
    
// 为了避免原数组发生改变
let array = [{ name: 'Anna', age: 16 }, { name: 'James', age: 18 }]
let newArray=array.map((item) => {
    const obj = { ...item, like:'eat'};
      return obj;
    })
    console.log(array); // [{ name: 'Anna', age: 16},{ name: 'James', age: 18}]
    console.log(newArray);//[{ name: 'Anna', age: 16,like: "eat"},{ name: 'James', age: 18,like: "eat"}]
```

### 2.['1', '2', '3'].map(parseInt)what & why ?

不是[1,2,3],而是**[1,NaN,NaN]**

```js
parseInt:
　　parseInt(numString, [radix])
```

**参数**
`numString`
必选项。要转换为数字的字符串。
`radix`
可选项。在 2 和 36 之间的表示 numString 所保存数字的进制的值。如果没有提供，则前缀为 '0x' 的字符串被当作十六进制，前缀为 '0' 的字符串被当作八进制。所有其它字符串都被当作是十进制的。如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。

**注释：**只有字符串中的第一个数字会被返回。

**注释：**开头和结尾的空格是允许的。如果 numString 的前缀不能解释为整数，则返回 NaN（而不是数字）

```js
parseInt("abc") // 返回 NaN。
parseInt("12abc") // 返回 12。
```

**所以map函数的第一个是字符串，下标作为基数去转换**

```js
parseInt('1', 0) //radix为0时，且string参数不以“0x”和“0”开头时，按照10为基数处理。这个时候返回1

parseInt('2', 1) //基数为1（1进制）表示的数中，最大值小于2，所以无法解析，返回NaN

parseInt('3', 2) //基数为2（2进制）表示的数中，最大值小于3，所以无法解析，返回NaN
```

map函数的参数是

arr.map(function(currentValue（当前值），index（下标），arr（可选，属于数组对象）),thisValue) 

所以

```js
['10','10','10','10','10'].map(parseInt);
// [10, NaN, 2, 3, 4]
```

### 3.基本类型和引用类型的区别

基本类型：**Number**, **String**, **Boolean**, **Undefined**, **Null**,**BigInt、Symbol**

引用类型：**Object(Function Array Date)**

（1）变量解析方法不同

基本类型是操作保存在变量中的值

引用类型如果是复制，则是操作对象的引用，添加属性则是操作实际对象

（2）区别

| 基本类型                                                     | 引用类型                                                   |
| :----------------------------------------------------------- | ---------------------------------------------------------- |
| 简单的数据段                                                 | 有多个值构成的对象                                         |
| 保存在栈中，占固定大小的空间                                 | 栈中存指向数据的引用，堆中存数据，大小不限                 |
| 不能给添加属性                                               | 可以给引用类型添加属性                                     |
| 复制变量：基本类型在栈内存中会形成新的变量，但是两者不会互相影响 | 复制的话栈内存多分配给新的变量，但是都指向堆中的同一个对象 |
| 传递参数：传递值，改变值                                     | 传递值，改变堆中的对象的属性                               |
| typeOf检测类型                                               | instanceOf判断是否在Object上                               |

（3）检测类型方法不同

typeOf检测基本类型，引用类型使用instanceOf（根据原型链判断的，所有引用类型的值都是Object的实例）

所以：instanceOf判断基本类型总是返回false

注意：

```js
null instanceof Object —>flase
typeof(null) —>Object
```

![image-20210413124825781](images/image-20210413124825781.png)

![image-20210413124833337](images/image-20210413124833337.png)

### 4.堆栈的区别

```
堆：由操作系统动态分配的内存，大小不定也不会自动释放，一般由程序员分配释放，也可由垃圾回收机制回收。

栈：由操作系统自动分配内存空间，自动释放，存储的是基础变量以及一些对象的引用变量，占据固定大小的空间。
```

1.栈：先进后出的数据结构，内存中存放变量的空间，堆：也是内存中的空间，但是存储变量的时候没有规律

2.栈存取快，不灵活，结构简单，内存回收容易；堆：存取比栈慢，因为要去堆中取得真的值，使用灵活，可以动态增加删除空间，存取慢

| 栈                                   | 堆                                     |
| ------------------------------------ | -------------------------------------- |
| 先进后出数据结构                     | 存储变量无规律                         |
| 存取快                               | 存取慢                                 |
| 不灵活(数据大小和生存周期确定)       | 灵活删除增加动态空间                   |
| 自动分配空间，自动释放空间，大小确定 | 自动分配空间，分配大小不定，不自动释放 |

### 5.箭头函数和普通函数的区别

1. 箭头函数没有 this、super、arguments，也没有new.target绑定。this、super、arguments以及函数内部的new.target的值由所在的，最靠近的非箭头函数来决定
2. 不能使用new调用：箭头函数没有[[Construct]]方法，因此不能被用为构造函数，使用new调用箭头函数会抛出错误
3. 没有原型：既然不能对箭头函数使用new，那么他也不需要原型，也就是没有prototype属性
4. 不能改变this：this的值在函数内部不能被修改。但是可以对箭头函数使用call，apply，bind（然而this并不会受到影响）

### 6.new一个对象发生了什么？

1. 创建一个新对象；
2. 将构造函数的作用域赋给新对象（因此this就指向了这个新对象）；
3. 执行构造函数中的代码（为这个新对象添加属性）；
4. 返回新对象

```js
function _new(fn, ...arg) {
  const obj = {}; //创建一个新的对象
  obj.__proto__ = fn.prototype; //把obj的__proto__指向fn的prototype,实现继承
  fn.apply(obj, arg) //改变this的指向
  return Object.prototype.toString.call(obj) == '[object Object]'? obj : {} //返回新的对象obj
 }
```

### 7.es6的不适用场景

优点是代码简洁，this提前定义

箭头函数没有它自己的this值，箭头函数内的this值继承自外围作用域

##### （1）**在对象上定义函数**

![image-20210307140325647](images/image-20210307140325647.png)

抛出错误，this.array是undefined，因为this指向了外围作用域

##### （2）**在原型上定义函数**

![image-20210307140513008](images/image-20210307140513008.png)

同理this指向window

##### （3）**动态上下文中的回调函数**

this是js中非常强大的特点，他让函数可以根据其调用方式动态的改变上下文，然后箭头函数直接在声明时就绑定了this对象，所以不再是动态的。

监听函数不可以使用箭头函数

![image-20210307141106403](images/image-20210307141106403.png)

这个回调的箭头函数是在全局上下文中被定义的，所以他的this是window

### 8.JS 中值的类型分为原始值类型和对象类型。

**原始值类型包括 number, string, boolean, null 和 undefined、symbol、bigint；对象类型（引用类型）即 object。首先原始值类型它就不是对象**。

另外，要**注意 'hello' 和 new String('hello') 的区别，前者是字符串字面值，属于原始类型，而后者是对象**。用 typeof 运算符返回的值也是完全不一样的：

```js
typeof 'hello';//string
typeof new String('hello');//object
```

之所以很多人分不清字符串字面值和 String 对象，归根结底就是 JS 的语法对你们太过纵容了。当执行 'hello'.length 时，发现可以意料之中的返回 5，你们就觉得 'hello' 就是 String 对象，不然它怎么会有 String 对象的属性。**其实，这是由于 JS 在执行到这条语句的时候，内部将 'hello' 包装成了一个 String 对象，执行完后，再把这个对象丢弃了，这种语法叫做 “装箱”**，在其他面向对象语言里也有（如 C#）。

### 9.OOP（面向对象编程）

现实生活的事物以及关系，抽象成类，通过继承，实现，组合的方式把万事万物都给容纳了。实现了对现实世界的抽象和数学建模

买菜自己做就是面向过程，去饭店喊老板做就是面向对象

优点：降低耦合性，提高维护度，提高可维护性（比如换成其他菜）![img](https://upload-images.jianshu.io/upload_images/1870221-f658a0eaa4e1a6e0.png?imageMogr2/auto-orient/strip|imageView2/2/w/982/format/webp)

1、封装
 隐藏对象的属性和实现细节，仅对外提供公共访问方式，将变化隔离，便于使用，提高复用性和安全性。
       2、继承
 提高代码复用性；继承是多态的前提。
       3、多态
 父类或接口定义的引用变量可以指向子类或具体实现类的实例对象。提高了程序的拓展性。

### 10.JavaScript特点

**解释性**：源代码不需要经过编译，而直接在浏览器中运行时被解释。

**基于对象**：能运用自己已经创建的对象。因此，许多功能可以来自于脚本环境中对象的方法与脚本的相互作用。

**事件驱动**：就是指在主页中执行了某种操作所产生的动作，此动作称为“事件”。比如按下鼠标、移动窗口、选择菜单等都可以视为事件。当事件发生后，可能会引起相应的事件响应

**跨平台**：JavaScript依赖于浏览器本身，与操作环境无关，只要能运行浏览器的计算机，并支持JavaScript的浏览器就可以正确执行。

**安全性**：JavaScript是一种安全性语言，它不允许访问本地的硬盘，并不能将数据存入到服务器上，不允许对网络文档进行修改和删除，只能通过浏览器实现信息浏览或动态交互。

**弱变量**：JavaScript语言中的变量是弱类型的，数据类型无须作出严格的要求。

**动态联编**：在程序运行时才能确定将要调用的函数。

**开发商**：JavaScript是网景公司开发的，Java是sun公司开发的。

### 11.事件委托

原理：事件冒泡

使用事件委托，只需在 DOM 树中尽量最高的层次上添加一个事件处理程序

最经典的就是ul和li标签的事件监听，比如我们在添加事件时候，采用事件委托机制，不会在li标签上直接添加，而是在ul父元素上添加。

https://blog.csdn.net/chenjuan1993/article/details/81347590

事件代理机制：如果每次点击一个li都绑定，性能会降低

```js
obj1.addEventListener('click',function(e){
   var e=e||window.event;
   if(e.target.nodeName.toLowerCase()=='h5'){
     alert(e.target.innerHTML);
 }，false);
```

使用target找到事件实际发生的元素

**阻止事件冒泡的方法：**

```js
//方式一：event.stopPropagation();
$("#div1").mousedown(function(event){
event.stopPropagation();
});
//方式二：return false;
$("#div1").mousedown(function(event){
return false;
});
```

但是这两种方式是有区别的。return false 不仅阻止了事件往上冒泡，而且阻止了事件本身。event.stopPropagation() 则只阻止事件往上冒泡，不阻止事件本身。

当`addEventListener`的第三个参数为`true`的时候，代表是在捕获阶段绑定，当第三个参数为`false`或者为空的时候，代表在冒泡阶段绑定。

`event.target`指向引起触发事件的元素，而`event.currentTarget`则是事件绑定的元素，只有被点击的那个目标元素的`event.target`才会等于`event.currentTarget`。

### 12.将原生ajax封装成promise

```js
var  myNewAjax=function(``url``){
	return new Promise(function(resolve,reject){
		var xhr = new XMLHttpRequest();
		xhr.open('get',url);
		xhr.send(data);
		xhr.onreadystatechange=function(){
		if(xhr.status==``200``&&readyState==``4``){
			var json=JSON.parse(xhr.responseText);
			resolve(json)
		}else if(xhr.readyState==``4``&&xhr.status!=``200``){
			reject(``'error'``);
	}
}
})
}
```

### 13.js加载过程阻塞，解决方法

指定script标签的async属性。

如果async="async"，脚本相对于页面的其余部分异步地执行（当页面继续进行解析时，脚本将被执行）

如果不使用async 且 defer="defer"：脚本将在页面完成解析时执行

![preview](images/view)

### 14.require模块化编程

如果加载多个js文件，加载时候会停止网页渲染，越多响应时间越长，其次由于js文件之间存在依赖关系，必须有加载顺序，依赖关系复杂的话代码维护会比较困难

require.js就是为了解决这个问题，

（1）实现js文件的异步加载，避免网页失去响应；

（2）管理模块之间的依赖性，便于代码的编写和维护。

主模块文件中写

```js
require(['moduleA', 'moduleB', 'moduleC'], function (moduleA, moduleB, moduleC){
// 主模块依赖这三个模块
　　});
```

require.js要求，每个模块是一个单独的js文件。加载多个模块，就会发出多次HTTP请求，会影响网页的加载速度。因此，require.js提供了一个[优化工具](http://requirejs.org/docs/optimization.html)，当模块部署完毕以后，可以用这个工具将多个模块合并在一个文件中，减少HTTP请求数。

#### （1）模块化分类

- es6: `import / export`
- commonjs: `require / module.exports / exports`
- amd: `require / defined`

#### （2）`require`与`import`的区别

- `require`支持 **动态导入**，`import`不支持，正在提案 (babel 下可支持)
- `require`是 **同步** 导入，`import`属于 **异步** 导入
- `require`是 **值拷贝**，导出值变化不会影响导入值；`import`指向 **内存地址**，导入值会随导出值而变化

### 15.babel原理及配置

#### （1）babel原理

`Babel` 能够转译 `ECMAScript 2015+` 的代码，使它在旧的浏览器或者环境中也能够运行。**相当于编译器**

抽象语法树（Abstract Syntax Tree, AST），`Babel` 本质上就是在操作 `AST` 来完成代码的转译。**（深度优先遍历）**

工作流程：

![image-20200830155824056](images/image-20200830155824056.png)

1. **Parse(解析)**：将源代码转换成更加抽象的表示方法（例如抽象语法树）

分为词法分析（拆解成独立个体括号、函数名、参数等）和语法分析（转换成AST树）

​	2.T**ransform(转换)**：对（抽象语法树）做一些特殊处理，让它符合编译器的期望

操作AST树，增删查改节点，换成真正需要的AST（深度优先遍历）

​	3.**Generate(代码生成)**：将第二步经过转换过的（抽象语法树）生成新的代码

#### （2）babel配置

babel6将babel全家桶拆分成了许多不同的模块

1.[babel-core](http://babeljs.io/docs/usage/api/)

看名字就知道，babel-core是作为babel的核心存在，babel的核心api都在这个模块里面，比如：transform。

2.[babel-cli](http://babeljs.io/docs/usage/cli/)

babel-cli是一个通过命令行对js文件进行换码的工具。

- 直接在命令行输出转译后的代码

  ```shell
    babel script.js
  ```

- 指定输出文件

  ```shell
    babel script.js --out-file build.js
   # 或者是
    babel script.js -o build.js
  ```

### 16.token流程

![image-20200901100200652](images/image-20200901100200652.png)

token可以预防CSRF攻击

#### （1）token特点：

1. 随机性：每次的token都是不一样的
2. 不可预测性：没有规律，无法预测
3. 时效性： 可以设置token的有效时间
4. 无状态、可扩展：由于只是一个算法，扩展起来非常方便
   生成Token的解决方案有许多，常用的一种就是 Json Web Tokens(JWT) .

#### （2）JWT(Json Web Tokens)

JWT标准的Tokens由三部分组成

1. header：包含token的类型和加密算法

2. payload：包含token的内容

3. signature：通过密钥将前两者加密得到最终的token

   这三部分中间使用 " . " 分隔开，并且都会使用Base64编码方式编码,如下
   eyJhbGc6IkpXVCJ9.eyJpc3MiOiJCIsImVzg5NTU0NDUiLCJuYW1lnVlfQ.SwyHTf8AqKYMAJc

使用方法：详细流程https://blog.csdn.net/qq_38796823/article/details/88207727

粗略如下：

安装（cnpm install jsonwebtoken）

引入（const jwt = require("jsonwebtoken") ;）

验证成功后生成一个token值

```js
let token = jwt.sign({username},'signature',{expiresIn:'1h'}) ;
//sign后面的三个参数分别是需要存储的信息，签名（最好用sha256加密后再用），过期时间
```

发送至客户端

```js
res.cookie("token",token) ;
//"token"（这里不要用token，一般情况下需要先进性加密处理）为name值；token为value值
```

验证token

```js
const islogin = (req,res) => {
    let{token} = req.body ;
    //解构赋值，获取请求的信息
	 jwt.verify(token,'signature',function(err,decoded){
	 //verify特定的验证方法（三个参数：请求的token，验证token是否是当前的token，函数体返回信息）
	        if(!err){
	        //如果token不存在进入
	            res.json({
	               ... ...
	                }
	            })
	        }else{
	        //否则，token存在进入
	            res.json({
	                ... ...
	                }
	            })
	        }
	 })
}
```

最后将返回值导出

```
module.exports = {
    islogin
}
```

### 17.前端安全XSS和CSRF

#### （1）**XSS**：（Cross Site Scripting）跨站脚本攻击

**反射型**（url参数直接注入）和**存储型**（存储到DB后读取时注入）

**预防：**

**XSS攻击防御**

（1）浏览器自带防御机制，主要应对反射型攻击（HTML内容或属性）：http响应头中自动添加x-xss-protection，值为0（关闭），1（打开），默认打开

（2）对特定字符做转义：内容注入替换尖括号（ < => &lt;  > => &gt; ） 属性注入替换单引号或双引号（ " => &quot; ' => &#39; ）

（3）CSP（Content Security Policy）内容安全策略：用于指定哪些内容可执行

```js
//我们可以在http响应头中设置Content-Security-Policy
//图片可以从任何地方加载(注意 "*" 通配符)
//多媒体文件仅允许从 media1.com 和 media2.com 加载(不允许从这些站点的子域名)
//可运行脚本仅允许来自于userscripts.example.com
Content-Security-Policy: default-src 'self'; img-src *; media-src media1.com media2.com; script-src userscripts.example.com

//同时meta中也支持设置Content-Security-Policy
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">
```

#### （2）**CSRF**：（Cross Site Request Forgy）跨站请求伪造

![image-20200831165625018](D:\code\jsWorkSpace\notes\Essay\HTML、CSS、JS学习笔记\JS知识点.assets\image-20200831165625018.png)

（1）用户在a站前端页面发起登录（身份认证）请求

（2）a站后端确认身份，登录成功，cookie中存在用户的身份认证信息

（3）b站前端页面向a站后端发起请求，带着a站的cookie信息（身份认证信息），请求成功

**CSRF攻击防御**：

（1）禁止第三方网站携带本网站的cookie信息：设置same-site属性，same-site属性有两个值，Strict（**所有的第三方请求都不能携带**本网站的cookie）和Lax（链接可以，但是**form表单提交和ajax请求不行**）

（2）本网站前端页面添加验证信息：使用**验证码**或者添加**token验证**

　　验证码：当发起请求时，前端需要输入本网站页面的验证码信息，后端对验证码进行验证，验证码正确才会进行相关操作（存取数据等）

　　token验证：a站前端将token存在当前页面中（比如表单中的input隐藏域，meta标签或者任何一个dom的属性）和cookie中，当请求a站后端的时候，参数中带上这个token字段，a站后端将参数中的token和cookie中的token做对比， 相同则验证通过，不同则请求不合法

（3）**referer验证**：禁止来自第三方的请求

（4）使用post请**求**：有一个说法是“post请求比get请求更安全”，那这种说法对不对呢？实际上这种说法并不准确，对于CSRF攻击来讲，不管是post还是get都能实现攻击，区别只是**post请求攻击方需要构造一个form表单才可以发起请求，比get请求（img的src， a标签的href等等）的攻击方式复杂**了一些，但是并不能有效的阻止攻击。

### 18.js内部对象

Navagator：提供有关浏览器的信息

Window：Window对象处于对象层次的最顶层，它提供了处理Navagator窗口的方法和属性

Location：提供了与当前打开的URL一起工作的方法和属性，是一个静态的对象

History：提供了与历史清单有关的信息

Document：包含与文档元素一起工作的对象，它将这些元素封装起来供编程人员使用

### 19.JavaScript的hoisting机制

变量和函数（函数声明的方式有提升机制，函数表达式没有这个机制）都有提升机制，而且**函数提升机制的优先级高于变量**的，这也就是说当函数名和变量名相同的时候,先提升函数

```js
var c = 2;//其实函数先被提升，然后被赋值为函数，然后又被赋值为变量2

 function c(){
　　c = 22;
　　console.log("c="+c);
 }
 c();//会报错，变量提升机制导致的（c is not a function）找不到c函数了
```

(1)参数是引用参数

```js
var foo={n:1};
(function (foo) {
    console.log(foo.n);
    foo.n=3;
    var foo={n:2};
    console.log(foo.n);
})(foo);
console.log(foo.n);
```

结果: 1 2 3	

(2)参数是传值参数

```js
var foo=1;
(function (foo) {
    console.log(foo);
    foo=3;
    var foo=2;
    console.log(foo);
})(foo);
console.log(foo);
```

结果:1 2 1

### 20.满足条件输出Ok?

### 

```javascript
 var a = ?   
 if(a == 1 & a ==2 & a ==3) {
     console.log("OK")
 }
1234
```

**答：方法一：**

**利用==号在比较数字和对象时会调用toString方法，然后重写toString方法**

```javascript
 a = {
	n: 0,
	toString：function() {
 		return ++this.n
	}
12345
```

**方法二：**

**也是重写toString方法，只是利用了数组了shift的特性**

```javascript
 a = [1, 2, 3]
 a.toString = a.shift
12
```

**方法三：**

**利用ES5新加的Object.defineProperty重写获取属性的get方法**

```javascript
var a, n = 0
Object.defineProperty(Window, "a", {
	get() {
		return ++n
	}
})
```

### 21.内存泄漏的情况

- 意外的**全局变量**: 无法被回收
- **定时器**: 未被正确关闭，导致所引用的外部变量无法被释放
- **事件监听**: 没有正确销毁 (低版本浏览器可能出现)
- **闭包**: 会导致父级中的变量无法被释放
- **dom 引用**: dom 元素被删除时，内存中的引用未被正确清空

可用 chrome 中的 timeline 进行内存标记，可视化查看内存的变化情况，找出异常点。

