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

