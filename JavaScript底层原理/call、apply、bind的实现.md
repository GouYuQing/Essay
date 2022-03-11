# JavaScript的call和apply

## 1.改变this指向

```js
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

## 2.call的模拟实现

除开call 还可以将bar变成foo的属性来实现

```js
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

```js
// 第一步
foo.fn = bar
// 第二步
foo.fn()
// 第三步
delete foo.fn
```

然后就可以手写call 函数

```js
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

```js
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

```js
var value = 1;
function bar(){
console.log(this.value);
}
bar.call(null)//指向了window，所以答案是1
```

```js
// 第三版
Function.prototype.call2 = function (context) {
    context = context || window;
    context.fn = this;
   /* var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    var result = eval('context.fn(' + args +')');*/
    // 将 context 后面的参数取出来
  	let args = [...arguments].slice(1);
  	// getValue.call(a, 'yck', '24') => a.fn('yck', '24')
  	let result = context.fn(...args);
    // 删除 fn
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

## 3.apply的模拟实现

同理call可得

```js
Function.prototype.myApply = function (context) {
  var context = context || window
  context.fn = this

  var result
  // 需要判断是否存储第二个参数
  // 如果存在，就将第二个参数展开
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }

  delete context.fn
  return result
}
```

## 4.bind的模拟实现

bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数

特点：1.**返回一个函数**2.可以传入参数

```js
var foo = {
    value: 1
};
function bar() {
	return this.value;
}
var bindFoo = bar.bind(foo);
console.log(bindFoo()); // 1
```

```js
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

```js
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

```js
//实现bind
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```


