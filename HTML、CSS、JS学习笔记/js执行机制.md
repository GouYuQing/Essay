# JavaScript执行机制学习



## 1.关于JavaScript

javascript是一门单线程语言，在最新的HTML5中提出了Web-Worker，但javascript是单线程这一核心仍未改变。所以一切javascript版的"多线程"都是用单线程模拟出来的。

## 2.JavaScript的同步和异步

同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

1、同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入Event Table并注册函数。

2、当Event Table中指定的事情完成时，会将这个函数移入Event Queue（一般是先进先出，但是有定时器的，就考虑达到执行要求之后的先进先出）。

3、主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。

4、上述过程会不断重复，也就是常说的Event Loop(事件循环)。

![image-20210923090920541](images/image-20210923090920541.png)

## 3.JavaScript宏任务和微任务

**macro-task(宏任务)**：包括整体代码script，setTimeout，setInterval、 I/O 操作、UI 渲染等

**micro-task(微任务)**：Promise.then，process.nextTick，Object.observe` ，`MutationObserver

注意：Promise立即执行，因为Promise是同步的函数，then函数分配到微任务**Event Queue**

不同类型的任务会进入对应的Event Queue。事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。

![image-20210923091025498](images/image-20210923091025498.png)

## 4.举个栗子

### （1）同步

```javascript
console.log(1);
console.log(2);
console.log(3);
/*
  ``执行结果：1、2、3
  ``同步任务，按照顺序一步一步执行
*/
```

### （2）同步和异步

```javascript
console.log(1);
setTimeout(function() {
  console.log(2);
},1000)
console.log(3);
/*
  执行结果：1、3、2
  同步任务，按照顺序一步一步执行
  异步任务，放入消息队列中，等待同步任务执行结束，读取消息队列执行
*/
```

```javascript
console.log(1);
setTimeout(function() {
  console.log(2);
},1000)
setTimeout(function() {
  console.log(3);
},0)
console.log(4);
/*
  猜测是：1、4、2、3  但实际上是：1、4、3、2
  分析：
    同步任务，按照顺序一步一步执行
    异步任务，当读取到异步任务的时候，将异步任务放置到Event table（事件表格）中，当满足某种条件或者说指定事情完成了（这里的是时间分别是达到了0ms和1000ms）当指定事件完成了才从Event table中注册到Event Queue（事件队列），当同步事件完成了，便从Event Queue中读取事件执行。（因为3的事情先完成了，所以先从Event table中注册到Event Queue中，所以先执行的是3而不是在前面的2）
*/
```

### （3）宏任务和微任务 

```javascript
console.log(1);
setTimeout(function() {
  console.log(2)
},1000);
new Promise(function(resolve) {
  console.log(3);
  resolve();
}
).then(function() {
  console.log(4)
});
console.log(5);
/*
  以同步异步的方式来判断的结果应该是：1、3、5、2、4
  但是事实上结果是：1、3、5、4、2
  为什么是这样呢？因为以同步异步的方式来解释执行机制是不准确的，更加准确的方式是宏任务和微任务：
  因此执行机制便为：执行宏任务 ===> 执行微任务 ===> 执行另一个宏任务 ===> 不断循环
    即：在一个事件循环中，执行第一个宏任务，宏任务执行结束，执行当前事件循环中的微任务，
执行完毕之后进入下一个事件循环中，或者说执行下一个宏任务*/
```

```javascript
//经典面试题
console.log(1);
setTimeout(function() {
  console.log(2);
  process.nextTick(function() {
    console.log('3');
  })
  new Promise(function(resolve) {
    console.log(4);
    resolve();
  }).then(function() {
    console.log('5')
  })
})
process.nextTick(function() {
  console.log('6');
})
new Promise(function(resolve) {
  console.log('7');
  resolve();
}).then(function() {
  console.log('8')
})
setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
/*1,7,6,8,2,4,3,5,9,11,10,12*/
```

### （4）async任务

async函数表示函数里面可能会有异步方法，await后面跟一个表达式，async方法执行时，遇到await会立即执行表达式，然后把表达式后面的代码放到微任务队列里，让出执行栈让同步代码先执行。

```js
async function async1() {
    console.log('async1 start');
    await async2();//await立即执行表达式，await后面的要放入微任务中
    console.log('async1 end');
}
async function async2() {
	console.log('async2');
}
console.log('script start');
setTimeout(function() {
    console.log('setTimeout');
}, 0)
async1();//同步队列中，宏任务
new Promise(function(resolve) {
    console.log('promise1');
    resolve();
}).then(function() {
    console.log('promise2');
});
console.log('script end');
/*
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
*/
```

## 5.Node的事件循环

Node.js的运行机制如下:

- V8引擎解析JavaScript脚本。
- 解析后的代码，调用Node API。
- libuv库负责Node API的执行。它将不同的任务分配给不同的线程，形成一个Event Loop（事件循环），以异步的方式将任务的执行结果返回给V8引擎。
- V8引擎再将结果返回给用户。

![image-20200502214306197](images/image-20200502214306197-1646662601670.png)

外部输入数据-->轮询阶段(poll)-->检查阶段(check)-->关闭事件回调阶段(close callback)-->定时器检测阶段(timer)-->I/O事件回调阶段(I/O callbacks)-->闲置阶段(idle, prepare)-->轮询阶段（Node 的 Event loop 分为 6 个阶段，按照该顺序反复运行）

#### （1）timer

timers 阶段会执行 `setTimeout` 和 `setInterval`

一个 `timer` 指定的时间并不是准确时间，而是在达到这个时间后尽快执行回调，可能会因为系统正在执行别的事务而延迟。

下限的时间有一个范围：`[1, 2147483647]` ，如果设定的时间不在这个范围，将被设置为 1。

#### （2）I/O

I/O 阶段会执行除了 close 事件，定时器和 `setImmediate` 的回调

#### （3）idle,prepare

idle，prepare阶段内部实现

#### （4）poll

poll 阶段很重要，这一阶段中，系统会做两件事情

1. 执行到点的定时器
2. 执行 poll 队列中的事件

并且当 poll 中没有定时器的情况下，会发现以下两件事情

- 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者系统限制
- 如果 poll 队列为空，会有两件事发生
  - 如果有 `setImmediate` 需要执行，poll 阶段会停止并且进入到 check 阶段执行 `setImmediate`
  - 如果没有 `setImmediate` 需要执行，会等待回调被加入到队列中并立即执行回调

如果有别的定时器需要被执行，会回到 timer 阶段执行回调

#### （5）check

check 阶段执行 `setImmediate`

#### （6）close callbacks

close callbacks 阶段执行 close 事件

```js
setTimeout(() => {
  console.log('setTimeout')
}, 0)
setImmediate(() => {
  console.log('setImmediate')
})
// 这里可能会输出 setTimeout，setImmediate
// 可能也会相反的输出，这取决于性能
// 因为可能进入 event loop 用了不到 1 毫秒，这时候会执行 setImmediate
// 否则会执行 setTimeout
```

```js
var fs = require('fs')

fs.readFile(__filename, () => {
  setTimeout(() => {
    console.log('timeout')
  }, 0)
  setImmediate(() => {
    console.log('immediate')
  })
})
// 因为 readFile 的回调在 poll 中执行
// 发现有 setImmediate ，所以会立即跳到 check 阶段执行回调
// 再去 timer 阶段执行 setTimeout
// 所以以上输出一定是 setImmediate，setTimeout
```

```js
setTimeout(()=>{
    console.log('timer1')
    Promise.resolve().then(function() {
        console.log('promise1')
    })
}, 0)
setTimeout(()=>{
    console.log('timer2')
    Promise.resolve().then(function() {
        console.log('promise2')
    })
}, 0)

/*浏览器端运行结果：timer1=>promise1=>timer2=>promise2*/

/*node端执行结果：如果是node11版本一旦执行一个阶段里的一个宏任务(setTimeout,setInterval和setImmediate)就立刻执行微任务队列，这就跟浏览器端运行一致，最后的结果为timer1=>promise1=>timer2=>promise2*/

/*如果是node10及其之前版本：要看第一个定时器执行完，第二个定时器是否在完成队列中。
如果是第二个定时器还未在完成队列中，最后的结果为timer1=>promise1=>timer2=>promise2
如果是第二个定时器已经在完成队列中，则最后的结果为timer1=>timer2=>promise1=>promise2(下文过程解释基于这种情况下)*/

```



## 6.总结

总体来说就是先执行一个宏任务，再执行所有微任务，一直循环，直到所有事件执行完毕

