# JavaScript执行上下文栈

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

![image-20220126230155286](images\image-20220126230155286.png)

如上图所示，上述代码经历了`全局执行上下文入栈->first入栈->second入栈->second出栈->first出栈->全局上下文出栈`

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

```
function foo() {
    console.log('foo1');
}
foo();  // foo2
function foo() {
    console.log('foo2');
}
foo(); // foo2
```

**（1）JavaScript可执行代码**

**全局代码、函数代码、eval代码**

执行到一个函数的时候，进行准备工作，叫做执行上下文(execution context)

 **（2）执行上下文栈（Execution context stack，ECS）**

用来管理多个执行上下文，是个数组

```
ECStack = [
    globalContext//全局代码最先入栈
];
```

```
function fun3() {
    console.log('fun3')
}
function fun2() {
    fun3();
}
function fun1() {
    fun2();
}
fun1();
开始进入栈
// fun1()
ECStack.push(<fun1> functionContext);

// fun1中竟然调用了fun2，还要创建fun2的执行上下文
ECStack.push(<fun2> functionContext);

//fun2还调用了fun3！
ECStack.push(<fun3> functionContext);

// fun3执行完毕
ECStack.pop();

// fun2执行完毕
ECStack.pop();

// fun1执行完毕
ECStack.pop();

// javascript接着执行下面的代码，但是ECStack底层永远有个globalContext
```

```
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

```
ECStack.push(<checkscope> functionContext);//checkscope入栈
ECStack.push(<f> functionContext);//调用了f，f入栈
ECStack.pop();//f出栈
ECStack.pop();//checkscope出栈
```

让我们模拟第二段代码：

```
ECStack.push(<checkscope> functionContext);//checkscope入栈
ECStack.pop();//立即执行函数出栈
ECStack.push(<f> functionContext);//f函数入栈
ECStack.pop();//f出栈
```

