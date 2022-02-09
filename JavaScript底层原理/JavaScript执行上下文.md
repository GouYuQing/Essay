# JavaScript执行上下文

## 1.什么是执行上下文

执行上下文是评估和执行 JavaScript 代码的环境的抽象概念

## 2.执行上下文类型

- 全局执行上下文

一个程序中只会有一个全局执行上下文。

- 函数执行上下文

每当一个函数被调用时, 都会为该函数创建一个新的上下文

- eval函数执行上下文

执行在 `eval` 函数内部的代码也会有它属于自己的执行上下文

执行上下文最明显的就是this的指向是执行时确定的。而作用域访问的变量是编写代码的结构确定的

作用域和执行上下文之间最大的区别是： **执行上下文在运行时确定，随时可能改变；作用域在定义时就确定，并且不会改变**。

## 3.怎么创建执行上下文

### （1）创建阶段

1. **this** 值的决定，即我们所熟知的 **This 绑定**。
2. 创建**词法环境**组件。
3. 创建**变量环境**组件

```js
ExecutionContext = {
  ThisBinding = <this value>,
  LexicalEnvironment = { ... },
  VariableEnvironment = { ... },
}
```

this指向：在全局执行上下文中，`this` 的值指向全局对象，在函数执行上下文中，`this` 的值取决于该函数是如何被调用的。

词法环境：一个词法环境由**环境记录器**和外部词法环境的引用组成

### （2）执行阶段



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

