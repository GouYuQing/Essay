# this及this指向问题

一门语言在运行的时候，需要一个`环境`，叫做`宿主环境`。对于JavaScript，宿主环境最常见的是`web浏览器`，浏览器提供了一个JavaScript运行的环境，这个环境里面，需要提供一些`接口`，好让`JavaScript引擎`能够和`宿主环境`对接

JavaScript引擎执行JavaScript代码

## 1.有哪些this

### （1）global this

- 在浏览器里，在全局范围内，this等价于window对象。

```js
//var声明的变量是全局的，然后相当于给this和window添加属性
var foo = "bar";
console.log(this.foo); //logs "bar"    console.log(window.foo); //logs "bar"
```

- 如果你声明变量时没有使用var或者let，就是在给全局的this添加属性
- 在node环境里，如果使用REPL(Read-Eval-Print Loop，简称REPL:读取-求值-输出,是一个简单的，交互式的编程环境)来执行程序,this并不是最高级的命名空间，最高级的是global.
  - 所以在node环境中，全局范围内，this不等于global
  - 在node的全局范围里，用var声明变量不会给变量添加this

### （2）function this

- 如果不是用new调用，在函数里面使用this都是指代全局范围的this。

```js
foo = "bar";
function testThis() {
	this.foo = "foo";
}
console.log(this.foo); //logs "bar"
testThis();
console.log(this.foo); //logs "foo"
```

- 使用严格模式，这时候this就会变成undefined。

```js
foo = "bar";
function testThis() {
    "use strict";
    this.foo = "foo";
} 
console.log(this.foo); //logs "bar"
testThis();  //Uncaught TypeError: Cannot set property 'foo' of undefined 
```

- 在函数中，正常方式调用函数，this指向全局变量，如果我们加一个new，这个函数就变成了一个构造函数，this指向这个实例

### （3）prototype this

- 创建的每一个函数都是函数对象，会自动获得一个特殊的属性prototype，可以给这个属性赋值。当你用new的方式调用一个函数的时候，你就能通过this访问你给prototype赋的值了。

- this可以获取prototype的值。当你在一个实例里面直接给this添加属性的时候，会隐藏prototype中与之同名的属性

- 把多个函数的prototype链接起来的从而形成一个原型链，this可以往上查找找到需要的值

- **JavaScript中可以嵌套函数，但是这个函数没有继承this**

  ```js
  function Thing() {
   }
  Thing.prototype.foo = "bar";
  Thing.prototype.logFoo = function () {
      var info = "attempting to log this.foo:";
    	function doIt() {//这是嵌套函数
       	console.log(info, this.foo);//不继承this，所以是没有定义的
      }
      doIt();
   }  
   var thing = new Thing();
   thing.logFoo();  //logs "attempting to log this.foo: undefined"
  ```

  为了使嵌套函数中有this，可以先放在一个变量里面

  ```js
  function Thing() {
   }
  Thing.prototype.foo = "bar";
      Thing.prototype.logFoo = function () {
      var self = this;
      var info = "attempting to log this.foo:";
    function doIt() {//这是嵌套函数
       	console.log(info, self.foo);//有this了
       }
      doIt();
   }  
   var thing = new Thing();
   thing.logFoo();  //logs "attempting to log this.foo: bar"
  ```

  但是如果把方法传递给一个函数的时候不起作用，所以需要绑定函数，使用bind

  ```js
  function Thing() {
  }
  Thing.prototype.foo = "bar";
  Thing.prototype.logFoo = function () { 
      console.log(this.foo);
  }
  function doIt(method) {
       method();
  } 
    var thing = new Thing();
    doIt(thing.lofFoo);//logs undefined不绑定就是undefined
    doIt(thing.logFoo.bind(thing)); //logs bar
  ```
  
  


- Object.create不会调用构造函数,可以通过使用Object.create来避免使用new，同样能够创建一个实例。

  ```js
   function Thing() {
  }
  Thing.prototype.foo = "bar";
  Thing.prototype.logFoo = function () {
  console.log(this.foo);
    } 
    var thing =  Object.create(Thing.prototype);
    thing.logFoo(); //logs "bar"
  ```

### （4）object this

- 在一个对象的一个函数里，你可以通过this来引用这个对象的其他属性

  ```js
  var obj = {
  	foo:'bar',
  	logFoo:function(){
  	console.log(this.foo);
  	}
  };
  obj.logFoo()//logs "bar"
  ```

- 即使没有使用new和Object.create，也没有使用函数创建对象，也可以将一个实例将函数绑定到上面去

  ```js
  var obj  = {
  	foo:"bar"
  };
  function logFoo(){
  	console.log(this.foo)
  }
  logFoo.apply(obj)//log "bar"
  ```

#### DOM event this

- 在一个HTML DOM事件处理程序里面，this始终指向这个处理程序被所绑定到的HTML DOM节点

#### HTML this

- 在HTML节点的属性里面，你可以放置JavaScript代码，this指向了这个元素

  ```html
  <div id="foo" onclick="console.log(this);"></div>
  //document.getElementById("foo").click(); //logs <div id="foo"...
  ```

#### override this

- 你不能重写this，因为它是保留字。

- 你可以通过eval来访问this

  ```
  function Thing () {
  }
  Thing.prototype.foo = "bar";
  Thing.prototype.logFoo = function () {
      eval("console.log(this.foo)"); //logs "bar"
  }
  //但是eval造成安全问题，所以一般用
  var thing = new Thing();
  thing.logFoo();
  ```

#### with this

- 你可以通过with来将this添加到当前的执行环境，并且读写this的属性的时候不需要通过this

```
function Thing () {
 }
 Thing.prototype.foo = "bar";
Thing.prototype.logFoo = function () {
     with (this) {//和使用变量添加是一样的
         console.log(foo);
         foo = "foo";//可以不使用this
     }
 }
 var thing = new Thing();
 thing.logFoo(); // logs "bar"
 console.log(thing.foo); // logs "foo"
```

#### jQuery this

- JQuery的this都指向HTML元素节点

```
<div class="foo bar1"></div>
$(".foo").on("click", function () {
      console.log(this); //logs <div class="foo...
});
```

#### thisArg this(???没用过不知道)

- thisArg 的函数参数来传递实例，这个函数参数会作为this的上下文

# this执向的情况

1. 构造函数被new调用的，this指向新构建的对象
2. call、apply、bind绑定的，this是明确的指向的对象
3. 用环境对象调用的（隐含绑定），指向环境对象，根据上下文来看
4. 默认的this，指向全局，strict模式下，是undefined
5. 原型上的this，谁调用this所在函数，this就指向谁

```js
var obj = {a: 1, b: function(){console.log(this);}}

1、作为对象调用时，指向该对象 obj.b(); // 指向obj
2、作为函数调用, var b = obj.b; b(); // 指向全局window
3、作为构造函数调用 var b = new Fun(); // this指向当前实例对象
4、作为call与apply调用 obj.b.apply(object, []); // this指向当前的object
```



# [JavaScript中嵌套函数的this关键字的理解](https://www.cnblogs.com/princeding/p/4438616.html)

我们都知道，在方法调用中，this指代的是方法所属的对象，如以下代码：

```
obj.test(console.log(this === obj)); //返回true
```

但是在嵌套函数中，情况就发生了很大变化。这需要分为两种情况：严格模式和非严格模式

```
//非严格模式下
var obj = {
    test:function (){
        var self = this;
        console.log(this === obj); //true
        f();
        function f(){
            console.log(this === obj); //false
            console.log(self === obj); //true
            console.log(this === window);  //true 非严格模式下嵌套函数指向全局，不能获取到本对象中的属性。
            };
        }
    };
obj.test();
```

```
//严格模式下
"use strict"
var obj = {
    test:function (){
        var self = this;
        console.log(this === obj);  //true
        f();
        function f(){
            console.log(this === obj);  //false
            console.log(self === obj);  //true
            console.log(this === window);  //false 严格模式下指向undefined
            console.log(this === undefined);  //true
            };
        }
    };
obj.test();
```

可以通过如下几种办法来解决此问题：
（1）定义变量，保存对象this

```
getAge: function () { 
		let self = this;
        setTimeout(function(){ alert(self.age)}, 1000)
    }
1234
```

(2)通过bind/call/apply方法改变函数this指向

```
getAge: function () { 
        setTimeout(function(){ alert(this.age)}.bind(this), 1000)
    }
123
```

(3)通过箭头函数的方式
由于箭头函数具有默认指向父级调用对象obj的特点，因此也可以解决本问题

```
getAge: function () { 
        setTimeout(() => { alert(this.age)}, 1000)
    }
```