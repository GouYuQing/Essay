#  柯里化

## 前置知识

柯里化函数是高阶函数的特殊应用

### （1）高阶函数（学习）

#### 满足条件

- 1.函数作为参数被传递：比如回调函数

- 2.函数作为返回值输出：让函数返回可执行函数，因为运算过程是可以延续的

#### 常用高阶函数

- 1.forEach()

```javascript
arr.forEach(item =>{
    if(item > 3){
        console.log(item); 
    }
});
```

- 2.map()

```javascript
const names = users.map(user => user.name);
```

- 3.Filter()

```javascript
const filtered = arr.filter(item => item < 3);
```

- 4.reduce()

```javascript
const sumOfAges = users.reduce((sum, user) => sum + user.age, 0);
```

- 5.sort()

```javascript
points.sort(function(a, b){
    return a-b
});
points.sort((a,b)=> b - a); 
```

- 6.every()

```javascript
numbers.every(function(currentValue) { 
    return currentValue < 10;
});// return true or false
```

- 7.some()

```javascript
numbers.some(function(currentValue) { 
    return currentValue < 10;
});// returns true or false
```

以上皆传递了函数作为参数

## 柯里化（简单理解）

### （1）什么是柯里化

柯里化（Currying），部分求值，为***实现多参数函数***提供一个递归降解的实现思路——把接受多个参数的函数变成接受单一参数的函数，并且返回接受余下参数而且返回结果的新函数。

在Haskell（纯函数编程）中，函数作为一等公民Currying是应函数式编程而生的。

#### 例子

实现加法的函数

```javascript
function add(x,y){
	return x+y;
}
function curryAdd(x){
	return function(y){
		return x+y;
	}
}
相当于 const add = x => y => x+y
```

在此实现中，Currying函数的返回值其实是一个接受剩余参数并且立刻返回计算值的函数， 即它的返回值并没有自动被 Currying化 。所以我们可以通过递归来将 currying 的返回的函数也自动 Currying化。 

```javascript
function curried(fn,...args){
	if(args.length>=fn.length){
		return fn(...args)
	}
}
return function(...args){
	return curried(fn,...args,...args2)
}
```

### （2）Currying使用场景

1. 参数复用，减少重复参数

   ```js
   // (1)减少重复参数
   function simpleURL(protocol, domain, path) {
     return `${protocol}://${domain}/${path}`
   }
   simpleURL('https', 'www.fxiaoke.com', 'bi')
   simpleURL('https', 'www.fxiaoke.com', 'crm')
   simpleURL('https', 'www.fxiaoke.com', 'paas')
   
   //（2）通过传参减少，但是如果域名什么的都要不同怎么办？？？就要用curry去实现
   function simpleURL(path) {
     return `https://www.fxiaoke.com/${path}`
   }
   simpleURL('bi')
   simpleURL('crm')
   simpleURL('paas')
   
   //（3）柯里化实现
   function simpleURL(protocol, domain, path) {
     return `${protocol}://${domain}/${path}`
   }
   const urlCurry = curry(simpleURL)('https')('www.fxiaoke.com')
   urlCurry('bi')
   urlCurry('crm')
   urlCurry('pass')
   ```

2. 延迟执行：bind和箭头函数可以实现延迟执行的功能

   一个柯里化的函数首先会接受一些参数，然后返回函数，这些函数不会立即计算，而是返回另一个函数，然后参数就被闭包保存，等函数真正求值的时候，参数被使用

3. 提前返回

### （3）柯里化实现（如下代码所示）

```js
const curry = (fn, arr = []) => (...args) => (
  arg => arg.length === fn.length ? fn(...arg): curry(fn, arg)
)([...arr, ...args])

let curryTest=curry((a,b,c,d)=>a+b+c+d)
curryTest(1,2,3)(4) //返回10
curryTest(1,2)(4)(3) //返回10
curryTest(1,2)(3,4) //返回10
```

### （4）总结

柯里化特点：

1. 逐步接收参数，并缓存供后期使用
2. 不立即执行，延后执行
3. 符合计算的条件，将缓存的参数，同意传递给执行方法
4. 延迟求值的特性需要用到JavaScript中的作用域来保存上一次传进来的参数。

缺点：如果调用链特别长， 会导致内存得不到释放

### （5）参考文献

（1）[JS函数式编程指南](http://shouce.jb51.net/js-function/index.html)







