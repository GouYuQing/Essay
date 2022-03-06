# 9.JavaScript的call和apply

改变this指向

```
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call(foo); // 1
```

注意两点：

1. call 改变了 this 的指向，指向到 foo
2. bar 函数执行了

模拟实现call

除开call 还可以将bar变成foo的属性来实现

```
var foo = {
    value: 1,
    bar: function() {
        console.log(this.value)
    }
};
foo.bar(); // 1
```

但是多加了属性，所以可以使用了之后选择删除

1. 将函数设为对象的属性
2. 执行该函数
3. 删除该函数

```
// 第一步
foo.fn = bar
// 第二步
foo.fn()
// 第三步
delete foo.fn
```

然后就可以手写call 函数

```
// 第一版
Function.prototype.call2 = function(context) {
    // 首先要获取调用call的函数，用this可以获取
    context.fn = this;
    context.fn();
    delete context.fn;
}
// 测试一下
var foo = {
    value: 1
};
function bar() {
    console.log(this.value);
}
bar.call2(foo); // 1
```

```
// 第二版
Function.prototype.call2 = function(context) {
    context.fn = this;
    var args = [];//假如传入参数不确定，就拿出数组来重新装参数
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');//从第二个参数开始才是
    }// 此时args为 ["arguments[1]", "arguments[2]", "arguments[3]"]
    eval('context.fn(' + args +')');//用eval（字符串）转换成JavaScript代码之后成为了context.fn(argument[1],argument[2].argument[3])
    delete context.fn;//删除
}
// 测试一下
var foo = {
    value: 1
};
function bar(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value);
}
bar.call2(foo, 'kevin', 18); //指向foo,传参
// kevin
// 18
// 1
```

**this 参数可以传 null，当为 null 的时候，视为指向 window**

```
var value = 1;
function bar(){
console.log(this.value);
}
bar.call(null)//指向了window，所以答案是1
```

```
// 第三版
Function.prototype.call2 = function (context) {
    context = context || window;
    context.fn = this;
    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    var result = eval('context.fn(' + args +')');
    delete context.fn
    return result;//context.fn(argument[1],argument[2].argument[3])
}
// 测试一下
var value = 2;
var obj = {
    value: 1
}
function bar(name, age) {
    console.log(this.value);
    return {
        value: this.value,
        name: name,
        age: age
    }
}
bar.call2(null); // 2，this指向了window
console.log(bar.call2(obj, 'kevin', 18));
// 1
// Object {
//    value: 1,
//    name: 'kevin',
//    age: 18
// }
```

call传参是多个参数，apply是多个参数组成的数组

# 10.JavaScript bind的模拟实现

bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数

特点：1.返回一个函数2.可以传入参数

```
var foo = {
    value: 1
};
function bar() {
	return this.value;
}
var bindFoo = bar.bind(foo);
console.log(bindFoo()); // 1
```

```
var foo = {
    value: 1
};
function bar(name, age) {
    console.log(this.value);
    console.log(name);
    console.log(age);

}
var bindFoo = bar.bind(foo, 'daisy');//传参
bindFoo('18');
// 1
// daisy
// 18
```

3.一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

```
//当 bind 返回的函数作为构造函数的时候，bind 时指定的 this 值会失效，但传入的参数依然生效。
var value = 2;
var foo = {
    value: 1
};
function bar(name, age) {
    this.habit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}
bar.prototype.friend = 'kevin';
var bindFoo = bar.bind(foo, 'daisy');
var obj = new bindFoo('18');//new 会将this指向obj
// undefined//所以原先的this绑定失效
// daisy
// 18
console.log(obj.habit);
console.log(obj.friend);
// shopping
// kevin
```

# 11. JavaScript new的模拟实现

new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一

第一版

```
// 第一版代码
function objectFactory() {
    var obj = new Object(),//新建一个对象obj
    Constructor = [].shift.call(arguments);//删除第一个参数之后，就是我们要传入的构造函数
    obj.__proto__ = Constructor.prototype;//原型指向构造函数的原型
    Constructor.apply(obj, arguments);//apply 改变构造函数this指向到新建的对象，obj就可以访问到构造函数中的属性
    return obj;
};
```

然后就相当于new一个对象

假如构造函数有返回值呢？

```
function Otaku (name, age) {
    this.strength = 60;
    this.age = age;
    return {//如果返回对象
        name: name,
        habit: 'Games'
    }
}
var person = new Otaku('Kevin', '18');
console.log(person.name) // Kevin
console.log(person.habit) // Games
console.log(person.strength) // undefined//现在只能得到返回对象中的属性
console.log(person.age) // undefined
```

```
function Otaku (name, age) {
    this.strength = 60;
    this.age = age;

    return 'handsome boy';//如果返回的是基本类型
}
var person = new Otaku('Kevin', '18');
console.log(person.name) // undefined//那就相当于没有返回值
console.log(person.habit) // undefined
console.log(person.strength) // 60
console.log(person.age) // 18
```

所以代码改进

```
// 第二版的代码
function objectFactory() {
    var obj = new Object(),
    Constructor = [].shift.call(arguments);
    obj.__proto__ = Constructor.prototype;
    var ret = Constructor.apply(obj, arguments);
    return typeof ret === 'object' ? ret : obj;//判断类型，如果是对象就返回对象，其他就返回原本的
};
```

# 12.JavaScript类数组对象与arguments

类数组：有length和若干索引的对象

```
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

![image-20200417161412685](D:\code\jsWorkSpace\notes\Essay\JavaScript底层原理\call、apply、new的实现.assets\image-20200417161412685.png)

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

# 13.JavaScript创建对象的多种方式以及优缺点（有一半看不懂）

1. 工厂模式

```
function createPerson(name) {
    var o = new Object();
    o.name = name;
    o.getName = function () {
        console.log(this.name);
    };
    return o;
}
var person1 = createPerson('kevin');
```

缺点：对象无法识别，因为所有的实例都指向一个原型

2. 构造函数模式

```
function Person(name) {
    this.name = name;
    this.getName = function () {
        console.log(this.name);
    };
}
var person1 = new Person('kevin');
```

优点：实例可以识别为一个特定的类型

缺点：每次创建实例时，每个方法都要被创建一次

2.1 构造函数模式优化

```
function Person(name) {
    this.name = name;
    this.getName = getName;
}

function getName() {
    console.log(this.name);
}

var person1 = new Person('kevin');
```

优点：解决了每个方法都要被重新创建的问题

缺点：这叫啥封装……

```
function Person(name) {

}

Person.prototype.name = 'keivn';
Person.prototype.getName = function () {
    console.log(this.name);
};

var person1 = new Person();
```

优点：方法不会重新创建

缺点：1. 所有的属性和方法都共享 2. 不能初始化参数

3.1 原型模式优化

```
function Person(name) {
}
Person.prototype = {
    name: 'kevin',
    getName: function () {
        console.log(this.name);
    }
};
var person1 = new Person();
```

优点：封装性好了一点

缺点：重写了原型，丢失了constructor属性

3.2 原型模式优化

```
function Person(name) {
}
Person.prototype = {
    constructor: Person,
    name: 'kevin',
    getName: function () {
        console.log(this.name);
    }
};
var person1 = new Person();
```

优点：实例可以通过constructor属性找到所属构造函数

缺点：原型模式该有的缺点还是有

4. 组合模式

构造函数模式与原型模式双剑合璧。

```
function Person(name) {
    this.name = name;
}
Person.prototype = {
    constructor: Person,
    getName: function () {
        console.log(this.name);
    }
};
var person1 = new Person();
```

优点：该共享的共享，该私有的私有，使用最广泛的方式

缺点：有的人就是希望全部都写在一起，即更好的封装性

4.1 动态原型模式

```
function Person(name) {
    this.name = name;
    if (typeof this.getName != "function") {
        Person.prototype.getName = function () {
            console.log(this.name);
        }
    }
}
var person1 = new Person();
```

注意：使用动态原型模式时，不能用对象字面量重写原型

```
function Person(name) {
    this.name = name;
    if (typeof this.getName != "function") {
        Person.prototype = {
            constructor: Person,
            getName: function () {
                console.log(this.name);
            }
        }

        return new Person(name);
    }
}

var person1 = new Person('kevin');
var person2 = new Person('daisy');

person1.getName(); // kevin
person2.getName();  // daisy
```

5.1 寄生构造函数模式

```
function Person(name) {

    var o = new Object();
    o.name = name;
    o.getName = function () {
        console.log(this.name);
    };

    return o;

}

var person1 = new Person('kevin');
console.log(person1 instanceof Person) // false
console.log(person1 instanceof Object)  // true
```

寄生构造函数模式，我个人认为应该这样读：

寄生-构造函数-模式，也就是说寄生在构造函数的一种方法。

也就是说打着构造函数的幌子挂羊头卖狗肉，你看创建的实例使用 instanceof 都无法指向构造函数

# 14.JavaScript继承的多种方式和优缺点（自闭）

1.原型链继承

引用类型的属性被所有实例共享，在创建Child的实例时，无法向Parent传参

```
function Parent () {
    this.names = ['kevin', 'daisy'];
}
function Child () {
}
Child.prototype = new Parent();
var child1 = new Child();
child1.names.push('yayu');
console.log(child1.names); // ["kevin", "daisy", "yayu"]
var child2 = new Child();
console.log(child2.names); // ["kevin", "daisy", "yayu"]
```

2.借用构造函数（经典继承）

优点：1.避免了引用类型的属性被所有实例共享

2.可以在 Child 中向 Parent 传参

```
function Parent（）{
	this.names = ['kevin', 'daisy'];
}
function Child(){
	Parent.call(this);
}
var child1 = new Child();
child1.names.push('yayu');
console.log(child1.names); // ["kevin", "daisy", "yayu"]
var child2 = new Child();
console.log(child2.names); // ["kevin", "daisy"]
```

缺点：

方法都在构造函数中定义，每次创建实例都会创建一遍方法。

3.组合继承

```
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}
Parent.prototype.getName = function () {
    console.log(this.name)
}
function Child (name, age) {
    Parent.call(this, name);  
    this.age = age;
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;
var child1 = new Child('kevin', '18');
child1.colors.push('black');
console.log(child1.name); // kevin
console.log(child1.age); // 18
console.log(child1.colors); // ["red", "blue", "green", "black"]
var child2 = new Child('daisy', '20');
console.log(child2.name); // daisy
console.log(child2.age); // 20
console.log(child2.colors); // ["red", "blue", "green"]
```

优点：融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式。

4.寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。

```
function createObj (o) {
    var clone = Object.create(o);//将传入的对象作为创建的对象的原型。
    clone.sayName = function () {
        console.log('hi');
    }
    return clone;
}
```

缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

5.寄生组合式继承

组合继承最大的缺点是会调用两次父构造函数。（见前面的组合继承代码）

一次是设置子类型实例的原型的时候：

```
Child.prototype = new Parent();
```

一次在创建子类型实例的时候：

```
var child1 = new Child('kevin', '18');
```

https://www.jb51.net/article/81766.htm

这个地址更容易理解我觉得，没那么复杂，也是六种方法

