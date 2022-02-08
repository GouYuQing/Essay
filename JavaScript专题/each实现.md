# JavaScript专题之jQuery通用遍历方法each的实现

```js
jQuery.each(object, [callback])//通用遍历方法，可用于遍历对象和数组
```

回调函数：对象成员或者数组索引+对应变量和内容

```js
// 遍历数组
$.each( [0,1,2], function(i, n){
    console.log( "Item #" + i + ": " + n );
});

// Item #0: 0
// Item #1: 1
// Item #2: 2

// 遍历对象
$.each({ name: "John", lang: "JS" }, function(i, n) {
    console.log("Name: " + i + ", Value: " + n);
});
// Name: name, Value: John
// Name: lang, Value: JS
```

实现each

```js
function each(obj, callback) {
    var length, i = 0;

    if (isArrayLike(obj)) {//是否是数组
        length = obj.length;
        for (; i < length; i++) {
            if (callback.call(obj[i], i, obj[i]) === false) {///回调函数为false的时候终止循环
                break;
            }
        }
    } else {
        for (i in obj) {
            if (callback.call(obj[i], i, obj[i]) === false) {
                break;
            }
        }
    }

    return obj;
}
```

for 循环的性能是明显好于 each 函数的，each 函数本质上也是用的 for 循环，但是为什么很慢？

each中call 会导致性能损失，但也正是 call 的存在，我们才能将 this 指向循环中当前的元素