# 11. JavaScript new的模拟实现

new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一

第一版

```js
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

```js
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

```js
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

```js
// 第二版的代码
function objectFactory() {
    var obj = new Object(),
    Constructor = [].shift.call(arguments);
    obj.__proto__ = Constructor.prototype;
    var ret = Constructor.apply(obj, arguments);
    return typeof ret === 'object' ? ret : obj;//判断类型，如果是对象就返回对象，其他就返回原本的
};
```

#