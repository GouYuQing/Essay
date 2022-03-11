# 12.JavaScript类数组对象与arguments

类数组：有length和若干索引的对象

```js
var array = ['name', 'age', 'sex'];
var arrayLike = {
    0: 'name',
    1: 'age',
    2: 'sex',
    length: 3
}
```

这两个数组在读写和长度方面很像，遍历也可以，但是类数组不能使用数组的方法，比如push

虽然没办法直接调用数组的方法，但是可以间接调用数组的方法

```
var arrayLike = {0: 'name', 1: 'age', 2: 'sex', length: 3 }
Array.prototype.join.call(arrayLike, '&'); // name&age&sex
Array.prototype.slice.call(arrayLike, 0); // ["name", "age", "sex"] // slice可以做到类数组转数组
Array.prototype.map.call(arrayLike, function(item){
    return item.toUpperCase();
}); 
// ["NAME", "AGE", "SEX"]
```

类数组转换成数组

```
var arrayLike = {0: 'name', 1: 'age', 2: 'sex', length: 3 }
// 1. slice
Array.prototype.slice.call(arrayLike); // ["name", "age", "sex"] 
// 2. splice
Array.prototype.splice.call(arrayLike, 0); // ["name", "age", "sex"] 
// 3. ES6 Array.from
Array.from(arrayLike); // ["name", "age", "sex"] 
// 4. apply
Array.prototype.concat.apply([], arrayLike)
```

前端中document.getElementByTagName()也返回类数组对象

Argument对象

![image-20200417161412685](images/image-20200417161412685.png)

Arguments对象的length属性，表示实参的长度，举个例子：

```
function foo(b, c, d){
    console.log("实参的长度为：" + arguments.length)
}

console.log("形参的长度为：" + foo.length)

foo(1)

// 形参的长度为：3
// 实参的长度为：1
```

callee属性，通过它可以调用函数自身

```
//关于闭包使用callee解决的办法
var data = [];
for (var i = 0; i < 3; i++) {
    (data[i] = function () {
       console.log(arguments.callee.i) 
    }).i = i;
}
data[0]();
data[1]();
data[2]();
// 0
// 1
// 2
```

接下来讲讲 argument和参数绑定

```
function foo(name, age, sex, hobbit) {
    console.log(name, arguments[0]); // name name
    // 改变形参
    name = 'new name';
    console.log(name, arguments[0]); // new name new name
    // 改变arguments
    arguments[1] = 'new age';
    console.log(age, arguments[1]); // new age new age
    // 测试未传入的是否会绑定 没有绑定
    console.log(sex); // undefined
    sex = 'new sex';
    console.log(sex, arguments[2]); // new sex undefined 形参没有传递所以undefined
    arguments[3] = 'new hobbit';
    console.log(hobbit, arguments[3]); // undefined new hobbit 形参直接被赋值了，但是实参还是没有的
}
foo('name', 'age')
```

es6转换数组很强势使用...运算符

```
function func(...arguments){
console.log(arguments);//[1,2,3]
}
func(1,2,3);
```

使用arguments的应用很多，我们会在 jQuery 的 extend 实现、函数柯里化、递归等场景看见 arguments 的身影

比如

1. 参数不定长
2. 函数柯里化
3. 递归调用
4. 函数重载