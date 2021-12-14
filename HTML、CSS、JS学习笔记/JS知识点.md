### 1.map会改变原数组吗？

map()为操作数组的一种方法，官方文档显示：

1. map() 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。
2. map() 方法按照原始数组元素顺序依次处理元素。
3. **注意：** map() 不会对空数组进行检测。
4. **注意：** map() 不会改变原始数组。

#### （1）当数组为基础类型的时候，原数组不变

```js
    let array=[1,2,3,4,5]
    let newArray=array.map((item) => item*2)
    console.log(array); // [1,2,3,4,5]
	console.log(newArray) //[2,4,6,8,10]
```

#### （2）当数组是引用类型的时候原数组改变

```js
 let array = [{ name: 'Anna', age: 16 }, { name: 'James', age: 18 }]
    let newArray=array.map((item) => {
      item.like='eat';
      return item;
    })
    console.log(array); // [{ name: 'Anna', age: 16,like: "eat"},{ name: 'James', age: 18,like: "eat"}]
    console.log(newArray);//[{ name: 'Anna', age: 16,like: "eat"},{ name: 'James', age: 18,like: "eat"}]
    
    // 为了避免原数组发生改变
    let array = [{ name: 'Anna', age: 16 }, { name: 'James', age: 18 }]
    let newArray=array.map((item) => {
      const obj = { ...item, like:'eat'};
      return obj;
    })
    console.log(array); // [{ name: 'Anna', age: 16},{ name: 'James', age: 18}]
    console.log(newArray);//[{ name: 'Anna', age: 16,like: "eat"},{ name: 'James', age: 18,like: "eat"}]
```

### 2.['1', '2', '3'].map(parseInt)what & why ?

不是[1,2,3],而是**[1,NaN,NaN]**

```js
parseInt:
　　parseInt(numString, [radix])
```

**参数**
`numString`
必选项。要转换为数字的字符串。
`radix`
可选项。在 2 和 36 之间的表示 numString 所保存数字的进制的值。如果没有提供，则前缀为 '0x' 的字符串被当作十六进制，前缀为 '0' 的字符串被当作八进制。所有其它字符串都被当作是十进制的。如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。

**注释：**只有字符串中的第一个数字会被返回。

**注释：**开头和结尾的空格是允许的。如果 numString 的前缀不能解释为整数，则返回 NaN（而不是数字）

```js
parseInt("abc") // 返回 NaN。
parseInt("12abc") // 返回 12。
```

**所以map函数的第一个是字符串，下标作为基数去转换**

```js
parseInt('1', 0) //radix为0时，且string参数不以“0x”和“0”开头时，按照10为基数处理。这个时候返回1

parseInt('2', 1) //基数为1（1进制）表示的数中，最大值小于2，所以无法解析，返回NaN

parseInt('3', 2) //基数为2（2进制）表示的数中，最大值小于3，所以无法解析，返回NaN
```

map函数的参数是

arr.map(function(currentValue（当前值），index（下标），arr（可选，属于数组对象）),thisValue) 

所以

```js
['10','10','10','10','10'].map(parseInt);
// [10, NaN, 2, 3, 4]
```

