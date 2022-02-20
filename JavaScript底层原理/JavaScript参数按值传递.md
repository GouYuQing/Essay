# JavaScript参数按值传递

## 1.概念

数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样。

ECMAScript中所有函数的参数都是按值传递的

## 2.按值传递

```js
var value = 1;
function foo(v) {
    v = 2;
    console.log(v); //2
}
foo(value);
console.log(value) // 1
```

传递value到foo里面，相当于拷贝了一份value，在foo函数里面操作拷贝后的value值，不影响原来的value值

## 3.引用传递

引用传递**传递对象的引用**，函数内部对参数的任何改变都会影响该对象的值，因为两者引用的是同一个对象

```js
//函数内部对参数的任何改变都会影响该对象的值，因为两者引用的是同一个对象。
var obj = {
    value: 1
};
function foo(o) {
    o.value = 2;//相当于 var o=obj, o.value = 2,obj.value = 2
    console.log(o.value); //2
}
foo(obj);
console.log(obj.value) // 2
```

但是红皮书上都写的是所有函数参数按值传递，为什么引用传递也可以成功呢？所以有了共享传递

## 4.共享传递

共享传递是指，在传递对象的时候，传递对象的引用的副本

```js
var obj = {
    value: 1
};
function foo(o) {
    o = 2;//o=obj
    console.log(o); //2
}
foo(obj);
console.log(obj.value) // 1
```

如果是引用传递但是又没有修改外层的值，所以这个例子是共享传递，例子2也是共享传递

**参数如果是基本类型是按值传递，如果是引用类型按共享传递**

但是又因为拷贝对象引用的副本，也是一种值的传递，所以直接认为也是按值传递

