# JavaScript闭包

MDN：闭包是指那些能够访问自由变量**（自由变量是指在函数中使用的，但既不是函数参数也不是函数的局部变量的变量。）**的函数

 **父函数被销毁** 的情况下，返回出的子函数的`[[scope]]`中仍然保留着父级的单变量对象和作用域链，因此可以继续访问到父级的变量对象，这样的函数称为闭包。

```
var a = 1;
function foo() {
    console.log(a);//foo函数可以访问变量a,但是a既不是foo的局部变量也不是参数，所以a是自由变量
}
foo();
```

所以 函数+函数可以访问的自由变量=闭包

**从技术的角度讲，所有的JavaScript函数都是闭包。**

但是实际的闭包是：

1. 即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）
2. 在代码中引用了自由变量

```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
var foo = checkscope();
foo();
//这个代码，在checkscope销毁之后，f函数依旧可以读取到 checkscope 作用域下的 scope 值，就是因为闭包
```

![image-20200416183610235](D:\code\jsWorkSpace\notes\knowledge\JavaScript底层原理学习.assets\image-20200416183610235.png)

![image-20200416183547197](D:\code\jsWorkSpace\notes\knowledge\JavaScript底层原理学习.assets\image-20200416183547197.png)

闭包与循环

闭包只存储外部变量的引用，而不会拷贝这些外部变量的值

```
function initEvents(){
  for(var i=1; i<=3; i++){
    $("#btn" + i).click(function showNumber(){
      alert(i);//4
    });
  }
}
initEvents();
```

我们创建了3个闭包，皆引用了同一个变量 `i`，且这三个闭包都是事件处理函数。由于变量 `i` 随着循环自增，因此最终输出的都是同样的值。

修复这个问题最简单的方法是在 `for` 语句块中使用 `let` 变量声明，这将在每次循环中为 `for` 语句块创建一个新的局部变量。如下：

```
function initEvents(){
  for(let i=1; i<=3; i++){
    $("#btn" + i).click(function showNumber(){
      alert(i);//1 2 3
    });
  }
}
initEvents();
复制代码
```

但是，如果变量声明在 `for` 语句块之外的话，即使用了 `let` 变量声明，所有的闭包还是会引用同一个变量，最终输出的还是同一个值。

 闭包意味着信息隐藏，可以创建私有状态的函数，使状态被封装起来

