# JavaScript执行上下文

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
```

1.执行全局代码，创建全局执行上下文，全局上下文被压入执行上下文栈

```
    ECStack = [
        globalContext
    ];
```

2.全局上下文初始化

```
    globalContext = {
        VO: [global],
        Scope: [globalContext.VO],
        this: globalContext.VO
    }
```

2.初始化的同时，checkscope 函数被创建，

```
    checkscope.[[scope]] = [//保存作用域链到函数的内部属性[[scope]]
      globalContext.VO
    ];
```

3.执行 checkscope 函数，创建 checkscope 函数执行上下文，

```
    ECStack = [
        checkscopeContext,//checkscope 函数执行上下文被压入执行上下文栈
        globalContext
    ];
```

4.checkscope 函数执行上下文初始化：

```
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

```
  ECStack = [
        fContext,//f 函数执行上下文被压入执行上下文栈
        checkscopeContext,
        globalContext
    ];
```

6.f 函数执行上下文初始化

```
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

