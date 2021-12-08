# es6

## 1.let 和 const

### 前置知识

var 声明的变量存在变量提升的特性

块级作用域存在于：

- 函数内部
- 块中( {  } 之间的区域)

### let 和 const 的特点：

#### (1)不会被提升

```
if (false) {
    let value = 1;
}
console.log(value); // Uncaught ReferenceError: value is not defined
```

#### (2)重复声明报错

```
var value = 1;
let value = 2; // Uncaught SyntaxError: Identifier 'value' has already been declared
```

#### (3)不绑定全局作用域

当在全局作用域中使用 var 声明的时候，会创建一个新的全局变量作为全局对象的属性。

```
var value = 1;
console.log(window.value); // 1
```

#### (4)值和类型是否可改变

let声明的变量可以改变，值和类型都可以改变，没有限制。

`const`声明的变量不得改变值，但是可以添加改变属性，只要指针不变就不会报错，不要把它指向新的堆内存，基本类型就不可变，引用类型就可以改变属性

**const必须立即初始化，只声明不赋值，就会报错。**

可以使用Object.freeze，防止改变属性【详情请查阅[Object.freeze](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)】

```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;//常量foo指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。
```

## 2.临时死区

临时死区(Temporal Dead Zone)，简写为 TDZ。

let 和 const 声明的变量不会被提升到作用域顶部，如果在声明之前访问这些变量，会导致报错：

```
console.log(typeof value); // Uncaught ReferenceError: value is not defined
let value = 1;
```

## 3.symbol

每个从`Symbol()`返回的symbol值都是唯一的。一个symbol值能作为对象属性的标识符；这是该数据类型仅有的目的

```js
var sym1 = Symbol();//不使用new创建
var sym2 = Symbol('foo');
var sym3 = Symbol('foo');
```

```js
Symbol("foo") === Symbol("foo"); // false
```

## 4.介绍下 Set、Map、WeakSet 和 WeakMap 的区别？

### （1）**set**：没有重复的值，set用来构造set数据结构

```javascript
const s = new Set();
[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
for (let i of s) {
  console.log(i);
}
// 2 3 5 4去重了

const set = new Set([1, 2, 3, 4, 4]);
[...set]//数组去重[...new Set(array)]扩展运算符加上set
// [1, 2, 3, 4]

//去除字符串里面的重复字符
[...new Set('ababbc')].join('')
// "abc"
```

```
set内部判断值是否相等类似是靠的精确运算符===，所以5和"5"是不一样的，但是他认为NaN===NaN的，所以NaN也会被去重，它也认为两个空对象是不一样的
```

**Set 结构的实例有以下属性**。

- `Set.prototype.constructor`：构造函数，默认就是`Set`函数。
- `Set.prototype.size`：返回`Set`实例的成员总数。

Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

- `Set.prototype.add(value)`：添加某个值，返回 Set 结构本身。
- `Set.prototype.delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `Set.prototype.has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `Set.prototype.clear()`：清除所有成员，没有返回值。

`Array.from`方法可以将 Set 结构转为数组。

```javascript
const items = new Set([1, 2, 3, 4, 5]);
const array = Array.from(items);
```

这就提供了去除数组重复成员的另一种方法。

```javascript
function dedupe(array) {
  return Array.from(new Set(array));
}

dedupe([1, 1, 2, 3]) // [1, 2, 3]
```

**遍历操作**

- `Set.prototype.keys()`：返回键名的遍历器
- `Set.prototype.values()`：返回键值的遍历器
- `Set.prototype.entries()`：返回键值对的遍历器
- `Set.prototype.forEach()`：使用回调函数遍历每个成员

keys(),values(),entires(),方法都是遍历器对象，set没有键名，或者说键名和键值是同一个

```javascript
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {//这是set默认的遍历器生成函数
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]

set.forEach((value, key) => console.log(key + ' : ' + value))；
//red:red
//green :green 
//blue:blue
```

Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。

```javascript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// 差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

### （2）**WeakSet**:和set是一样的，也是不重复的，但是只能是不重复的对象

不能放数值和symbol对象

WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中

**方法和属性**

- **WeakSet.prototype.add(value)**：向 WeakSet 实例添加一个新成员。
- **WeakSet.prototype.delete(value)**：清除 WeakSet 实例的指定成员。
- **WeakSet.prototype.has(value)**：返回一个布尔值，表示某个值是否在

没有size属性，不能遍历成员：因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了

### （3）**Map**

“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

```javascript
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```

构造函数的时候相当于

```javascript
const items = [
  ['name', '张三'],
  ['title', 'Author']
];
const map = new Map();
items.forEach(
  ([key, value]) => map.set(key, value)//设置键值对的值
);
```

`Set`和`Map`都可以用来生成新的 Map。

```javascript
const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
m1.get('foo') // 1

const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
m3.get('baz') // 3
```

对同一个键多次赋值，后面的值将覆盖前面的值。

读取一个未知的键，则返回`undefined`。

1.Map 转为数组

```javascript
const myMap = new Map()
  .set(true, 7)
  .set({foo: 3}, ['abc']);
[...myMap]
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```

2.数组 转为 Map：将数组传入 Map 构造函数，就可以转为 Map。

```javascript
new Map([
  [true, 7],
  [{foo: 3}, ['abc']]
])
// Map {
//   true => 7,
//   Object {foo: 3} => ['abc']
// }
```

3.对象转换成map

```javascript
let obj = {"a":1, "b":2};
let map = new Map(Object.entries(obj));//通过Object.entries()
```

4.Map 转为 JSON

Map 转为 JSON 要区分两种情况。一种情况是，Map 的键名都是字符串，这时可以选择转为对象 JSON。

```javascript
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
// '{"yes":true,"no":false}'
```

另一种情况是，Map 的键名有非字符串，这时可以选择转为数组 JSON。

```javascript
function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}

let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'JSON 转为 Map
```

5.JSON 转为 Map，正常情况下，所有键名都是字符串。

```javascript
function jsonToStrMap(jsonStr) {
  return objToStrMap(JSON.parse(jsonStr));
}

jsonToStrMap('{"yes": true, "no": false}')
// Map {'yes' => true, 'no' => false}
```

整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组

```javascript
function jsonToMap(jsonStr) {
  return new Map(JSON.parse(jsonStr));
}

jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']}
```

### （4）**WeakMap**：和Map相似，用于生成键值对的集合

```javascript
// WeakMap 可以使用 set 方法添加成员
const wm1 = new WeakMap();
const key = {foo: 1};
wm1.set(key, 2);
wm1.get(key) // 2

// WeakMap 也可以接受一个数组，
// 作为构造函数的参数
const k1 = [1, 2, 3];
const k2 = [4, 5, 6];
const wm2 = new WeakMap([[k1, 'foo'], [k2, 'bar']]);
wm2.get(k2) // "bar"
```

与Map区别：只接受对象作为键名（null除外），不接受其他类型的值

```
const map = new WeakMap();
map.set(1,2);
// TypeError: 1 is not an object!
map.set(Symbol(),2);
// TypeError: Invalid value used as weak map key
map.set(null, 2)
// TypeError: Invalid value used as weak map key
```

和WeakSet是一样的

一旦不再需要这两个对象，我们就必须手动删除这个引用，否则垃圾回收机制就不会释放占用的内存。一旦忘了写，就会造成内存泄露。

WeakMap 键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。

基本上，如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。一个典型应用场景是，在网页的 DOM 元素上添加数据，就可以使用`WeakMap`结构。当该 DOM 元素被清除，其所对应的`WeakMap`记录就会自动被移除。

## 5.迭代器和for…of

### 1.Iterator（遍历器）概念

JavaScript 原有的表示“集合”的数据结构，主要是数组（`Array`）和对象（`Object`），Set,Map

统一的接口机制，来处理所有不同的数据结构。遍历器（Iterator）就是这样一种机制。

遍历器对象本质上就是一个指针对象，调用next指向下一个成员（返回value和done）

模仿next：

```javascript
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
```

### 2.处理所有的数据结构，for…of循环时，会调用Iterator接口

一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。

```javascript
const obj = {
  [Symbol.iterator] : function () {
    return {
      next: function () {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
```

```javascript
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();//部署在arr的Symbol.iterator属性上

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```

### 3.调用iterator的场合

**（1）解构赋值**

对数组和 Set 结构进行解构赋值时，会默认调用`Symbol.iterator`方法。

```javascript
let set = new Set().add('a').add('b').add('c');

let [x,y] = set;
// x='a'; y='b'

let [first, ...rest] = set;
// first='a'; rest=['b','c'];
```

**（2）扩展运算符**

扩展运算符（...）也会调用默认的 Iterator 接口。

```javascript
// 例一
var str = 'hello';
[...str] //  ['h','e','l','l','o']

// 例二
let arr = ['b', 'c'];
['a', ...arr, 'd']
// ['a', 'b', 'c', 'd']
```

**（3）yield\***

`yield*`后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。

```javascript
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
iterator.next() // { value: 2, done: false }
iterator.next() // { value: 3, done: false }
iterator.next() // { value: 4, done: false }
iterator.next() // { value: 5, done: false }
iterator.next() // { value: undefined, done: true }
```

## 6.ES6声明变量的六种方法

`var`、`function`、`let`和`const`、`import`命令和`class`命令

## 7.global和window

顶层对象，在浏览器环境指的是`window`对象，在 Node 指的是`global`对象。ES5 之中，顶层对象的属性与全局变量是等价的。

ES6 开始，全局变量将逐步与顶层对象的属性脱钩。

```javascript
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```

**globalThis 对象**

- 浏览器里面，顶层对象是`window`，但 Node 和 Web Worker 没有`window`。

- 浏览器和 Web Worker 里面，`self`也指向顶层对象，但是 Node 没有`self`。

- Node 里面，顶层对象是`global`，但其他环境都不支持。

  

  为了能够在各种环境，都能取到顶层对象，现在一般是使用`this`变量，但是有局限性。

全局环境中，`this`会返回顶层对象。但是，Node.js 模块中`this`返回的是当前模块，ES6 模块中`this`返回的是`undefined`。

```javascript
//取到全局对象的方法
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};
```

## 8.解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构

### 1.数组

```javascript
let [a, b, c] = [1, 2, 3];
```

如果解构不成功，变量的值就等于`undefined`。

```javascript
let [foo] = [];
let [bar, foo] = [1];
```

### 2.对象

```javascript
let { foo, bar } = { foo: 'aaa', bar: 'bbb' };
foo // "aaa"
bar // "bbb"
```

数组有次序，对象没有次序，按照属性来确定值

### 3.字符串

```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```

类似数组的对象都有一个`length`属性，因此还可以对这个属性解构赋值。

```javascript
let {length : len} = 'hello';
len // 5
```

### 4.数值和布尔值

解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。

```javascript
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```

上面代码中，数值和布尔值的包装对象都有`toString`属性，因此变量`s`都能取到值。

解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

```javascript
let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
```

### 5.函数

```javascript
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```

在解构赋值中，尽量不要用圆括号

### 6.用途

**（1）交换变量的值**

```javascript
let x = 1;
let y = 2;

[x, y] = [y, x];
```

**（2）从函数返回多个值**

```javascript
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();
```

**（3）输入模块的指定方法**

加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。

```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```

**（4）提取方法**

比如

```
const {useState,useEffect} from react.....
```

## 9.Promise

Promise 是异步编程的一种解决方案

特点：

1.对象的状态不受外界影响。有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）

2.一旦状态改变，就不会再变，任何时候都可以得到这个结果

缺点：

1.无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。

2.如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。

3.当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

## 10.Generator函数

异步编程解决方案：执行 Generator 函数会返回一个遍历器对象

`function`关键字与函数名之间有一个星号；函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”），yield只能用在Generator函数里面

```javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();//调用next方法使得指针移向下一个状态
//yield表达式是暂停执行的标记，而next方法可以恢复执行。
```

```javascript
 { value: 'ending', done: true }//一个value,一个done
//yield后面的表达式123 + 456，不会立即求值，只会在next方法将指针移到这一句时，才会求值。
//yield，函数暂停执行，下一次再从该位置继续向后执行，而return语句不具备位置记忆的功能
```

任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。

比如`for...of`循环可以自动遍历 Generator 函数运行时生成的`Iterator`对象，且此时不再需要调用`next`方法。

```javascript
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
```

## 11.async

是 Generator 函数的语法糖

```javascript
const fs = require('fs');

const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

上面代码的函数`gen`可以写成`async`函数，就是下面这样。

```javascript
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

一比较就会发现，`async`函数就是将 Generator 函数的星号（`*`）替换成`async`，将`yield`替换成`await`，仅此而已。

### 1.**改进方面：**

（1）内置执行器

不需要调用next方法，就可以自动执行，得到结果

（2）语义清晰

`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

（3）返回值是promise

`async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。

`async`函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而`await`命令就是内部`then`命令的语法糖。

### 2.**基本用法**

`async`函数返回一个 Promise 对象，可以使用`then`方法添加回调函数。当函数执行的时候，一旦遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

```javascript
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```

**语法**

（1）返回promise对象，`async`函数返回一个 Promise 对象。`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

```javascript
async function f() {
  return 'hello world';
}

f().then(v => console.log(v))
// "hello world"
```

```javascript
async function f() {
  throw new Error('出错了');
}

f().then(
  v => console.log('resolve', v),
  e => console.log('reject', e)
)//async函数内部抛出错误，会导致返回的 Promise 对象变为reject状态。抛出的错误对象会被catch方法回调函数接收到。
//reject Error: 出错了
```

（2）`await`命令后面是一个 Promise 对象，返回该对象的结果。如果不是 Promise 对象，就直接返回对应的值。

```javascript
async function f() {
  // 等同于
  // return 123;
  return await 123;
}

f().then(v => console.log(v))
// 123
```

await 后面的promise语句变成reject，则整个async函数都会中断执行，为了防止第二个不执行，可以使用try…catch捕获错误，多个await都可以放在try…catch中捕获

```javascript
async function f() {
  await Promise.reject('出错了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');//现在第二个await会执行了
}

f()
.then(v => console.log(v))
// 出错了
// hello world
```

实现原理：Generator 函数和自动执行器，包装在一个函数里。

## 12.promise、generator、async三者比较

| 优缺点 | promise                                                      | generator                                                    | async                                                        |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 优点   | 1.回调地狱解决办法2.状态改变就不会再变，任何时候都能得到确切的结果3.代码完全都是 Promise 的 API（`then`、`catch`等等） | 1.语义比 Promise 写法更清晰2.接近于同步编程，并且代码量很少  | 1.实现最简洁，最符合语义2.做到了串行的同步写法3.代码清晰明了.，写法符合思维逻辑 |
| 缺点   | 1.一旦创建，立即执行，中途无法取消2.不设置回调函数，内部错误无法反映到外部3.处于pending状态时，无法得知状态 | 必须有一个任务运行器，自动执行 Generator 函数，上面代码的`spawn`函数就是自动执行器，它返回一个 Promise 对象，而且必须保证`yield`语句后面的表达式，必须返回一个 Promise。 | 并行不占优势                                                 |

## 	13. 延展操作符(...)

####  应用

```js
1. 浅拷贝
var arr = [1, 2, 3];
var arr2 = [...arr]; // 等同于 arr.slice()

2. 数组合并
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
var arr3 = [...arr1, ...arr2];

3.字符串转成数组
[...'hello'] // [ "h", "e", "l", "l", "o" ]
```

## 	14. 指数操作符(**)

等同于Math.pow()

```js
2**10 --> Math.pow(2,10)
```

## 	15. `Object.keys()`

​	`Object.keys()`、`Object.values()`和 `Object.entries()`  `Object.fromEntries()`

应用：

``` js
const obj = {a: 1, b: 2, c: 3};
Object.keys(obj); // ['a','b','c'] 取key值
Object.entries(obj); // [['a', 1], ['b', 2], ['c', 3]] 取key和value
Object.values(obj); // [1,2,3] 取value

Object.fromEntries()是entries()的逆向操作
Object.fromEntries([['a', 1], ['b', 2], ['c', 3]])；// {a: 1, b: 2, c: 3}
```

## 16. 链判断操作符 (?.)----ES2020

​	在链式调用的时候判断，左侧的对象是否为`null`或`undefined`。如果是的，就不再往下运算，而是返回`undefined`

​	判断对象方法是否存在，如果存在就立即执行

```js
iterator.return?.()
```

**应用**

```js
// ?.运算符相当于一种短路机制，只要不满足条件，就不再往下执行。
a?.[++x]
// 等同于
a == null ? undefined : a[++x]

delete a?.b
// 等同于
a == null ? undefined : delete a.b

(a?.b).c
// 等价于
(a == null ? undefined : a.b).c
```

**报错情况**

```js
// 构造函数
new a?.()
new a?.b()
// 链判断运算符的右侧有模板字符串
a?.`{b}`
a?.b`{c}`
// 链判断运算符的左侧是 super
super?.()
super?.foo
// 链运算符用于赋值运算符左侧
a?.b = c
```

## 	17. Null判断操作符(??)

​	运算符左侧的值为`null`或`undefined`时，返回右侧的值

```js
let user = { name: 'tomas', age: 19, isAdmin: false };
let userName = user.name ?? 'tom'; // 'tomas'
let sex = user.sex ?? 1; // 1

// 和 || 比较
// 值转为boolean 是否为false，是则返回右侧的值
if(user.isAdmin || user.name==='tomas'){
    console.log('我是管理员');
}
```

`??`有一个运算优先级问题，它与`&&`和`||`的优先级孰高孰低。现在的规则是，如果多个逻辑运算符一起使用，必须用括号表明优先级，否则会报错。

```js
// 任意两个结合 直接如下这样写会报错
lhs && middle ?? rhs

// 必须加入优先级括号
(lhs && middle) ?? rhs
```

**应用：**

```vue
1. 
<template slot-scope="scope">
	{{ scope.row.comment ?? "--" }}
</template>
 2. 配合 ?. 使用，当没target的时候赋初值 
<script>
    let person ={
		sun: {
            age:20
        }
    }
    person.sun.address ?? 'xxx';
</script>
```

## 	18.  字符串新增的一些方法

**includes()**：返回布尔值，表示是否找到了参数字符串。

**startsWith()**：返回布尔值，表示参数字符串是否在原字符串的头部。

**endsWith()**：返回布尔值，表示参数字符串是否在原字符串的尾部。

```js
let s = 'Hello world!';
s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true

// 支持第二个参数表示开始搜索的位置
s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

**repeat()**：`repeat`方法返回一个新字符串，表示将原字符串重复`n`次

```javascript
'hello'.repeat(2) // "hellohello"
```

 **padStart()**：把指定字符串填充到字符串头部，返回新字符串

 **padEnd()**：把指定字符串填充到字符串尾部，返回新字符串

##### 		应用：数值前后补零

```js
const FillZero = (num, len) => num.toString().padStart(len, "0");
const num = FillZero(169, 5);
// num => "00169"
```

**trimStart() / trimLeft()**:  去掉字符串前面的空格，返回新字符串

**trimEnd() / trimRight()**  : 去掉字符串后面的空格，返回新字符串

```javascript
const s = '  abc  ';
s.trim() // "abc"
s.trimStart() // "abc  "
s.trimEnd() // "  abc"
```

## 19. 可选catch

```js
// pre
try{
    ...
}catch(e){
    
}
   
// now
try{
...
}catch{
    
}
```

## 20.数组相关方法

1. Array.form() 将类数组与可迭代对象转化为数组
2. Array.isArray(target) 判断target是否为数组

### (1) 改变原数组

1. push 数组末尾加一项，返回新数组长度
2. unshift 数组开头加一项，返回新数组长度
3. pop 去除数组末尾项，返回当前项，
4. shift 去除数组开头项，返回当前项，
5. reverse 颠倒数组顺序 
6. sort 对数组进行从小到大（数值大小，字符串头字母a-z）排序 ，
7. flat(deep) 数组扁平化
   * deep = inifinity，数组扁平化到一维数组
8. splice 插入、删除、替换数组。返回被删除的项目

**`arr.splice(index,howmany,item1,.....,itemX)`**

```js
// 1. 指定位置增加项
var array = [1,2,3,4,5];
var array2 = array.splice(1,0,["新增","xinzeng"]);
// 下标1的位置，增加添加的项 
// array [1,["新增","xinzeng"],2,3,4,5]  
// array2 []

// 2. 删除指定位置，并新增项
var array = [1,2,3,4,5];
var array2 = array.splice(1,1,["新增","xinzeng"]);
// 下标1的位置，删除1项，并新增1项
// array [1,["新增","xinzeng"],3,4,5]  
// array2 [2]

3. 删除
var array = [1,2,3,4,5];
var array2 = array.splice(3);
// 从下标3的位置开始删除
// array [1,2,3]  
// array2  [4,5]
```

### (2)不改变原数组

1. join 将一个数组（或一个类数组对象）的所有元素连接成一个字符

```js
  const ary = ['九', '九', 'c'];
    let ary = ary.join();       // "a,b,c"
    let ary = ary.join(', ');   // "a, b, c"
    let ary = ary.join(' + ');  // "a + b + c"
    let ary = ary.join('');     // "abc"
```

1. slice 返回数组一部分

   * slice( startIdx , endIdx ) 
   * slice( index )
   * slice() 浅拷贝数组

2. concat 合并，返回新数组

3. indexOf  返回数组中指定元素**第一次**出现的下标，没有则返-1。 **满足条件后不再进行循环**

4. lastIndexOf  返回数组中指定元素**最后一次**出现的下标，没有则返-1

5. find()  数组中**第一个**满足所提供回调函数的那一项，都不满足则返回 undefined。**满足条件后不再进行循环**

6. findIndex()  数组中通过提供回调函数的**第一个元素**的索引。若都未通过则返回-1

7. reduce()

   ```js
   // 应用：
   // 累加
   // 数组降维
   [[2,5],[1,3]].reduce((pre,cur)=>pre.concat(cur))
   // 数组中元素出现的次数
   // 数组去重  [...new Set(arr)]
   // 跟了个初始空数组 不然concat将报错
   [1,2,2,3,5,4,4,3].reduce((pre,cur)=> pre.includes(cur)? pre: pre.concat(cur),[])
   ```

8. map 返回新数组

9. forEach()

* forEach 和 map 的区别：
  * map返回新数组，原数组不会改变，可链式调用
  * forEach返回undefined
* 相同之处：都不能终止循环，除非报错

9. fliter() 返回所有满足条件的数组项组成的新数组
10. every() 测试数组内的所有元素是否都能满足回调函数中的条件，返回true/false
11. some() 测试数组中是不是至少有1个元素通过了回调函数的条件，返回true/false

## 21.一些编程小技巧

**转时间戳**

```js
const timestamp = +new Date("2019-02-14");// 1550102400000
```

**向下取整**

```js
Math.floor(1.5)
// 可选择下面写法
~~1.5; // 1
1.6 | 1; // 1
```

**是否为空对象**

```js
const obj = {};
const flag = !Array.isArray(obj) && !Object.keys(obj).length;
// ------- 
JSON.stringfy({}) === '{}';
```

**交换赋值**

```js
let a = 0;
let b = 1;
[a, b] = [b, a];
// a b => 1 0
```

**过滤空值(Boolean后为false的)**

```js
const arr = [undefined, null, "", 0, false, NaN, 1, 2];
arr.filter(Boolean); // [1,2]
// 等价于
arr.filter(item => {return Boolean(item)})
```

**创建指定长度且值相等的数组**

```js
const arr = new Array(3).fill(0);
// arr => [0, 0, 0]
```

## 22.参考资料

### （1）es6入门教程

### （2）[推荐文章：你会用ES6，那倒是用啊！](https://juejin.cn/post/7016520448204603423)

