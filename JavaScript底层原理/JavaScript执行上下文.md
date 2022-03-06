# JavaScript执行上下文和执行上下文栈

## 1.什么是执行上下文

执行上下文是评估和执行 JavaScript 代码的环境的抽象概念，JavaScript 代码执行一段可执行代码(executable code)时，会创建对应的执行上下文(execution context)。

## 2.执行上下文类型

- 全局执行上下文

一个程序中只会有一个全局执行上下文。

- 函数执行上下文

每当一个函数被调用时, 都会为该函数创建一个新的上下文

- eval函数执行上下文

执行在 `eval` 函数内部的代码也会有它属于自己的执行上下文

执行上下文最明显的就是this的指向是执行时确定的。而作用域访问的变量是编写代码的结构确定的

作用域和执行上下文之间最大的区别是： **执行上下文在运行时确定，随时可能改变；作用域在定义时就确定，并且不会改变**。

## 3.执行上下文包括什么？

对于每个执行上下文，都有三个重要属性：

- **变量对象(Variable object，VO)**
- **作用域链(Scope chain)**
- **this**

### （1）变量对象

变量对象，是执行上下文中的一部分，可以抽象为一种 **数据作用域**，其实也可以理解为就是一个简单的对象，它存储着该执行上下文中的所有 **变量和函数声明(不包含函数表达式)**。

#### a.全局上下文的变量对象

**全局对象是什么？**

1. 可以通过 this 引用，在客户端 JavaScript 中，全局对象就是 Window 对象。

```js
console.log(this);
```

2. 全局对象是由 Object 构造函数实例化的一个对象。

```js
console.log(this instanceof Object);
```

3. 预定义了一堆函数和属性。

```js
// 都能生效
console.log(Math.random());
console.log(this.Math.random());
```

4. 作为全局变量的宿主。

```js
var a = 1;
console.log(this.a);
```

5. 客户端 JavaScript 中，全局对象有 window 属性指向自身。

```js
var a = 1;
console.log(window.a);

this.window.b = 2;
console.log(this.b);
```

全局上下文中的变量对象就是全局对象

#### b.函数上下文的变量对象

在函数上下文中，我们用活动对象(activation object, AO)来表示变量对象（VO）。（活动对象就是变量对象，**VO进入执行上下文之后被激活，变成AO**）

活动对象是在进入函数上下文时刻被创建的，它通过函数的 arguments 属性初始化。arguments 属性值是 Arguments 对象（**调用函数时，会为其创建一个Arguments对象，并自动初始化局部变量arguments，指代该Arguments对象。所有作为参数传入的值都会成为Arguments对象的数组元素**。）。

```js
function foo(a) {
  var b = 2;
  function c() {}
  var d = function() {};
  b = 3;
}
foo(1);
//进入执行上下文之后，AO为
AO = {
    arguments: {
        0: 1,
        length: 1
    },
    a: 1,
    b: undefined,
    c: reference to function c(){},
    d: undefined
}
//执行之后
AO = {
    arguments: {
        0: 1,
        length: 1
    },
    a: 1,
    b: 3,
    c: reference to function c(){},
    d: reference to FunctionExpression "d"
}
//总结
//全局上下文的变量对象初始化是全局对象

// 函数上下文的变量对象初始化只包括 Arguments 对象

// 在进入执行上下文时会给变量对象添加形参、函数声明、变量声明等初始的属性值

//在代码执行阶段，会再次修改变量对象的属性值
```

##### **举个例子**

###### ①第一题

```js
function foo() {
    console.log(a);
    a = 1;
}
foo(); // ???
function bar() {
    a = 1;
    console.log(a);
}
bar(); // ???
```

第一段会报错：`Uncaught ReferenceError: a is not defined`。

第二段会打印：`1`。

这是因为函数中的 "a" 并没有通过 var 关键字声明，所有不会被存放在 AO 中。

第一段执行 console 的时候， AO 的值是：

```js
AO = {
    arguments: {
        length: 0
    }
}
```

没有 a 的值，然后就会到全局去找，全局也没有，所以会报错。

当第二段执行 console 的时候，全局对象已经被赋予了 a 属性，这时候就可以从全局找到 a 的值，所以会打印 1。

###### ②第二题

```js
console.log(foo);
function foo(){
    console.log("foo");
}
var foo = 1;
```

会打印函数，而不是 undefined 。

这是因为在进入执行上下文时，首先会处理函数声明，其次会处理变量声明，如果如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性。

**函数提升（函数一等公民）>变量提升**

### （2）作用域链

当查找变量的时候，会先从**当前上下文的变量对象**中查找，如果没有找到，就会从父级(词法层面上的父级)**执行上下文的变量对象中查找**，一直找到全局上下文的变量对象，也就是**全局对象**。这样由多个执行上下文的变量对象构成的链表就叫做**作用域链**。（作用域链可以理解为一组对象列表，包含 **父级和自身的变量对象**，因此我们便能通过作用域链访问到父级里声明的变量或者函数。）

函数有一个内部属性 [[scope]]，函数创建的时候，就会保存所有父变量对象到其中

```js
function foo() {
    function bar() {
        ...
    }
}
//函数创建时，各自的作用域链为
foo.[[scope]] = [
  globalContext.VO
];

bar.[[scope]] = [
    fooContext.AO,
    globalContext.VO
];
//函数激活之后，进入函数上下文，创建 VO/AO 后，就会将活动对象添加到作用链的前端
作用域链表示为Scope=[AO].concat([Scope]);
```

#### **举个栗子：**总例子：（关于变量对象和执行上下文和作用域链）

```js
var scope = "global scope";
function checkscope(){
    var scope2 = 'local scope';
    return scope2;
}
checkscope();
```

1.函数创建，保存作用域链

```js
checkscope.[[scope]] = [globalContext.VO]
```

2..执行 checkscope 函数，创建 checkscope函数执行上下文，被压入栈中

```js
ECStack= [ checkscopeContext,globalContext];
```

3.函数做准备工作

（1）创建函数作用域链

```js
checkscopeContext={Scope:checkscope.[[scope]]}
```

（2）创建活动对象，初始化活动对象，加入形参、函数声明、变量声明

```js
checkscopeConetxt={
	AO:{
		arguments:{
		length: 0
		},                     
		scope2: undefined
		},
	Scope:checkscope.[[scope]]
}
```

（3）将活动对象压入 checkscope 作用域链顶端

```js
checkscopeContext = {
    AO: {
        arguments: {
            length: 0
        },
        scope2: undefined
    },
    Scope: [AO, [[Scope]]]
}
```

4.执行函数,修改AO

```js
checkscopeContext = {
    AO: {
        arguments: {
            length: 0
        },
        scope2: 'local scope'
    },
    Scope: [AO, [[Scope]]]
}
```

5.找到了scope2的值，函数执行完毕，函数上下文从栈中弹出

```js
ECStack = [
    globalContext
];
```

### （3）this指向(从ECMAScript规范解读this )

 ECMAScript 5.1 规范地址：

英文版：http://es5.github.io/#x15.1

中文版：http://yanhaijing.com/es5/#115

#### 1.ECMAScript 的类型分类

分为语言类型和规范类型。

语言类型：Undefined, Null, Boolean, String, Number, 和 Object

规范类型：Reference, List, Completion, Property Descriptor, Property Identifier, Lexical Environment, 和 Environment Record。（是用来用算法描述 ECMAScript 语言结构和 ECMAScript 语言类型的，它们的作用是用来描述语言底层行为逻辑）

其中Reference和this的指向有关

Reference:用来解释诸如 delete、typeof 以及赋值等操作行为的（为了描述语言的底层行为而存在的，但是并没有存在于实际的代码中）

#### 2.Reference组成

- base value（属性所在对象，Number,undefined,Object里面）
- referenced name(属性名称)
- strict reference（是否是严格模式）

```javascript
//比如下面这个例子
var foo = {
    bar: function () {
        return this;
    }
}; 
foo.bar(); // foo
// bar对应的Reference是：
var BarReference = {
    base: foo,//所在对象是foo
    propertyName: 'bar',//名称是bar
    strict: false//不是严格模式
};
```

##### a.获取 Reference 组成部分的方法，比如 **GetBase 和 IsPropertyReference**

1.GetBase

返回 reference 的 base value。

2.IsPropertyReference

简单的理解：如果 base value 是一个对象，就返回true。

3.getValue

从 Reference 类型获取对应值的方法

```js
var foo = 1;
var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
};
GetValue(fooReference) // 1;
```

#### 3.如何确定this的值

当函数调用时，如何确定this的值？

1.计算 MemberExpression 的结果赋值给 ref

2.判断 ref 是不是一个 Reference 类型

```js
2.1 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)

2.2 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)**其实也是undefined**

2.3 如果 ref 不是 Reference，那么 this 的值为 undefined
```

###### （1）什么是MemberExpression？

MemberExpression：

- PrimaryExpression // 原始表达式 可以参见《JavaScript权威指南第四章》
- FunctionExpression // 函数定义表达式
- MemberExpression [ Expression ] // 属性访问表达式
- MemberExpression . IdentifierName // 属性访问表达式
- new MemberExpression Arguments // 对象创建表达式

```js
function foo() {
    return function() {
        console.log(this)
    }
}
foo()(); // MemberExpression 是 foo()
var foo = {
    bar: function () {
        return this;
    }
}
foo.bar(); // MemberExpression 是 foo.bar
// 总结如下：所以MemberExpression就是（）左边的部分
```

###### （2）判断`MemberExpression`返回的结果是不是`Reference`类型

判断`MemberExpression`是不是一个`Reference`类型

```js
var value = 1;

var foo = {
  value: 2,
  bar: function () {
    return this.value;
  }
}

//示例1
console.log(foo.bar()); // 2
//示例2
console.log((foo.bar)());// 2
//示例3
console.log((foo.bar = foo.bar)()); //1
//示例4
console.log((false || foo.bar)());// 1
//示例5
console.log((foo.bar, foo.bar)());// 1
```

所以 这样来看

```js
var Reference = {
  base: foo,
  name: 'bar',
  strict: false
};
//现在来判断MemberExpression返回的结果是不是Reference类型，IsPropertyReference（XXX）是true，那this的值就是GetBase(ref)
```

base value是foo，foo是一个对象， IsPropertyReference(ref) 结果为true

又因为`this = GetBase(MemberExpression返回的结果)`

```js
this = GetBase(ref);
```

`this = GetBase(foo),`所以this指向foo

示例1和示例2是一样的结果,this指向foo,结果是2

实例3 (foo.bar = foo.bar)()

使用了赋值操作符，使用了GetValue，所以不是Reference类型，不是就是this指向undefined

this 为 undefined，非严格模式下，this 的值为 undefined 的时候，其值会被隐式转换为全局对象。所以结果是1，如果是严格模式下，报错

实例4(false || foo.bar)()

或运算符，使用了GetValue，所以不是Reference类型，不是就是this指向undefined，同例子3，结果是1

实例5 (foo.bar, foo.bar)()

逗号操作符，同上所述，结果是1，

###### （3）举个栗子

所以不能简单理解成this就是调用函数的对象，否则如下案例就不对

```js
var value = 1;

var foo = {
  value: 2,
  bar: function () {
    return this.value;
  }
}
console.log((false || foo.bar)()); // 1
```

如果简单理解的话，那就是this指向调用的对象，就是2，但是实际是1

## 3.如何存储执行上下文？

执行上下文被存储在栈结构里面，被称为执行上下文栈。

### （1）执行上下文栈（Execution context stack，ECS）

执行上下文栈，也就是在其它编程语言中所说的“调用栈”，是一种拥有 LIFO（后进先出）数据结构的栈，被用来存储代码运行时创建的所有执行上下文。

遇到函数调用时会逐个入栈，执行栈顶元素，一个一个出栈，控制流程到达执行上下文

```js
let a = 'Hello World!';
function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}
function second() {
  console.log('Inside second function');
}
first();
console.log('Inside Global Execution Context');
```

![image-20220126230155286](image-20220126230155286.png)

如上图所示，上述代码经历了`全局执行上下文入栈->first入栈->second入栈->second出栈->first出栈->全局上下文出栈`

#### 举个例子

```js
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
```

1.执行全局代码，创建全局执行上下文，全局上下文被压入执行上下文栈

```js
    ECStack = [
        globalContext
    ];
```

2.全局上下文初始化

```js
    globalContext = {
        VO: [global],
        Scope: [globalContext.VO],
        this: globalContext.VO
    }
```

2.初始化的同时，checkscope 函数被创建，

```js
    checkscope.[[scope]] = [//保存作用域链到函数的内部属性[[scope]]
      globalContext.VO
    ];
```

3.执行 checkscope 函数，创建 checkscope 函数执行上下文，

```js
    ECStack = [
        checkscopeContext,//checkscope 函数执行上下文被压入执行上下文栈
        globalContext
    ];
```

4.checkscope 函数执行上下文初始化：

```js
checkscopeContext = {
        AO: {//活动对象
            arguments: {//形参
                length: 0
            },
            scope: undefined,//变量声明
            f: reference to function f(){}//函数声明
        },
        Scope: [AO, globalContext.VO],//作用域链
        this: undefined//暂不知道指向
    }
```

5.执行 f 函数，创建 f 函数执行上下文，

```js
  ECStack = [
        fContext,//f 函数执行上下文被压入执行上下文栈
        checkscopeContext,
        globalContext
    ];
```

6.f 函数执行上下文初始化

```js
 fContext = {
        AO: {//活动对象
            arguments: {//形参
                length: 0
            }
        },
        Scope: [AO, checkscopeContext.AO, globalContext.VO],//作用域链
        this: undefined//指向未明
    }
```

7.f 函数执行，沿着作用域链查找 scope 值，返回 scope 值

8.f 函数执行完毕，f 函数上下文从执行上下文栈中弹出

9.checkscope 函数执行完毕，checkscope 执行上下文从执行上下文栈中弹出

10.全局执行上下文代码弹出结束

JavaScript 引擎并非一行一行地分析和执行程序，而是一段一段地分析执行。当执行一段代码的时候，会进行一个“准备工作”，比如第一个例子中的变量提升，和第二个例子中的函数提升。

```js
var foo = function () {
    console.log('foo1');
}
foo();  // foo1
var foo = function () {
    console.log('foo2');
}
foo(); // foo2
```

```js
function foo() {
    console.log('foo1');
}
foo();  // foo2
function foo() {
    console.log('foo2');
}
foo(); // foo2
```

#### 再举个例子

用来管理多个执行上下文，是个栈

```js
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();

var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```

两段代码执行的结果一样，都是打印local scope，但是两段代码执行上下文栈的变化不一样。

让我们模拟第一段代码：

```js
ECStack.push(<checkscope> functionContext);//checkscope入栈
ECStack.push(<f> functionContext);//调用了f，f入栈
ECStack.pop();//f出栈
ECStack.pop();//checkscope出栈
```

让我们模拟第二段代码：

```js
ECStack.push(<checkscope> functionContext);//checkscope入栈
ECStack.pop();//立即执行函数出栈
ECStack.push(<f> functionContext);//f函数入栈
ECStack.pop();//f出栈
```

## 4.执行上下文生命周期

- 创建阶段
- 执行阶段
- 销毁阶段

### （1）创建阶段

1. 确定**this** 的值：在创建上下文的时候绑定，指向取决于函数怎么调用，或者全局中指向window
2. 进行变量和函数的初始化声明。
3. 构建作用域链

### （2）执行阶段

js开始执行语句，对定义的变量赋值、顺着作用域链访问变量、如果内部有函数调用就创建一个新的执行上下文压入执行栈并把控制权交出

### （3）销毁阶段

当前执行上下文（局部环境）会被弹出执行上下文栈并且销毁，控制权被重新交给执行栈上一层的执行上下文。

但是对于闭包来说，当闭包的父函数执行完成后，父函数本身执行环境的作用域链会被销毁，但是由于闭包的作用域链仍然在引用父函数的变量对象，导致了父函数的变量对象会一直驻存于内存，无法销毁，除非闭包的引用被销毁，闭包不再引用父函数的变量对象，这块内存才能被释放掉。过度使用闭包会造成 **内存泄露** 的问题。

闭包理解：[见《JavaScript闭包》](./JavaScript闭包.md)



