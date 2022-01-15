# JavaScript变量对象

变量对象，是执行上下文中的一部分，可以抽象为一种 **数据作用域**，其实也可以理解为就是一个简单的对象，它存储着该执行上下文中的所有 **变量和函数声明(不包含函数表达式)**。

JavaScript 代码执行一段可执行代码(executable code)时，会创建对应的执行上下文(execution context)。

对于每个执行上下文，都有三个重要属性：

- 变量对象(Variable object，VO)
- 作用域链(Scope chain)
- this

（1）全局上下文

在客户端 JavaScript 中，全局对象就是 Window 对象。全局对象是由 Object 构造函数实例化的一个对象。

```
console.log(this);
```

全局上下文中的变量对象就是全局对象

（2）函数上下文

在函数上下文中，我们用活动对象(activation object, AO)来表示变量对象（VO）。（活动对象就是变量对象，**VO进入执行上下文之后被激活，变成AO**）

活动对象是在进入函数上下文时刻被创建的，它通过函数的 arguments 属性初始化。arguments 属性值是 Arguments 对象（**调用函数时，会为其创建一个Arguments对象，并自动初始化局部变量arguments，指代该Arguments对象。所有作为参数传入的值都会成为Arguments对象的数组元素**。）。

```
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

```

（3）执行过程

1. 进入执行上下文
2. 代码执行

```
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

```
AO = {
    arguments: {
        length: 0
    }
}
```

没有 a 的值，然后就会到全局去找，全局也没有，所以会报错。

当第二段执行 console 的时候，全局对象已经被赋予了 a 属性，这时候就可以从全局找到 a 的值，所以会打印 1。

2.第二题

```
console.log(foo);
function foo(){
    console.log("foo");
}
var foo = 1;
```

会打印函数，而不是 undefined 。

这是因为在进入执行上下文时，首先会处理函数声明，其次会处理变量声明，如果如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性。