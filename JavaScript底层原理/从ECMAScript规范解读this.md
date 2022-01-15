# 从ECMAScript规范解读this （需要多看几遍）

 ECMAScript 5.1 规范地址：

英文版：http://es5.github.io/#x15.1

中文版：http://yanhaijing.com/es5/#115

ECMAScript 的类型分为语言类型和规范类型。

语言类型：Undefined, Null, Boolean, String, Number, 和 Object

规范类型：Reference, List, Completion, Property Descriptor, Property Identifier, Lexical Environment, 和 Environment Record。

Referce:用来解释诸如 delete、typeof 以及赋值等操作行为的（为了描述语言的底层行为而存在的，但是并没有存在于实际的代码中）

组成：

- base value（属性所在对象，Number,undefined,Object里面）
- referenced name(属性名称)
- strict reference

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

获取 Reference 组成部分的方法，比如 **GetBase 和 IsPropertyReference**

1.GetBase

返回 reference 的 base value。

2.IsPropertyReference

简单的理解：如果 base value 是一个对象，就返回true。

下面开始讲解this

根据规范中写道的是

1.计算 MemberExpression 的结果赋值给 ref

2.判断 ref 是不是一个 Reference 类型

```
2.1 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)

2.2 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)**其实也是undefined**

2.3 如果 ref 不是 Reference，那么 this 的值为 undefined
```

现在了解 MemberExpression是属性访问表达式？

- PrimaryExpression // 原始表达式 可以参见《JavaScript权威指南第四章》
- FunctionExpression // 函数定义表达式
- MemberExpression [ Expression ] // 属性访问表达式
- MemberExpression . IdentifierName // 属性访问表达式
- new MemberExpression Arguments // 对象创建表达式

```
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
总结如下：所以MemberExpression就是（）左边的部分
```

现在来判断MemberExpression返回的结果是不是Reference类型

![image-20200416152424529](D:\code\jsWorkSpace\notes\knowledge\JavaScript底层原理学习.assets\image-20200416152424529.png)

所以 这样来看

```
var Reference = {
  base: foo,
  name: 'bar',
  strict: false
};
现在来判断MemberExpression返回的结果是不是Reference类型，IsPropertyReference（XXX）是true，那this的值就是GetBase(of)
```

base value是foo，foo是一个对象，所以为true

this = GetBase(MemberExpression返回的结果)

this = GetBase(foo),所以this指向foo

实例2和实例1是一样的结果

实例3 (foo.bar = foo.bar)()

使用了赋值操作符，使用了GetValue，所以不是Reference类型，不是就是this指向undefined

this 为 undefined，非严格模式下，this 的值为 undefined 的时候，其值会被隐式转换为全局对象。

实例4(false || foo.bar)()

或运算符，使用了GetValue，所以不是Reference类型，不是就是this指向undefined，同例子3

实例5 (foo.bar, foo.bar)()

逗号操作符，同上所述