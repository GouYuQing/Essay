# JavaScript作用域链

当查找变量的时候，会先从**当前上下文的变量对象**中查找，如果没有找到，就会从父级(词法层面上的父级)**执行上下文的变量对象中查找**，一直找到全局上下文的变量对象，也就是**全局对象**。这样由多个执行上下文的变量对象构成的链表就叫做**作用域链**。（作用域链可以理解为一组对象列表，包含 **父级和自身的变量对象**，因此我们便能通过作用域链访问到父级里声明的变量或者函数。）

函数有一个内部属性 [[scope]]，函数创建的时候，就会保存所有父变量对象到其中

```
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

总例子：（关于变量对象和执行上下文和作用域链）

```
var scope = "global scope";
function checkscope(){
    var scope2 = 'local scope';
    return scope2;
}
checkscope();
```

1.函数创建，保存作用域链

checkscope.[[scope]] = [globalContext.VO]

2.函数执行上下文，被压入栈中

```
ECStack= [ checkscopeContext,globalContext];
```

3.函数做准备工作

（1）创建函数作用域链

```
checkscopeContext={Scope:checkscope.[[scope]]}
```

（2）创建活动对象，初始化

```
checkscopeConetxt={
	AO:{
		arguments:{
		length:0
		},
		scope2:undefined
		},
	Scope:checkscope.[[scope]]
}
```

（3）将活动对象压入 checkscope 作用域链顶端

```
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

checkscopeContext = {
    AO: {
        arguments: {
            length: 0
        },
        scope2: 'local scope'
    },
    Scope: [AO, [[Scope]]]
}

5.找到了scope2的值，函数执行完毕，函数上下文从栈中弹出

