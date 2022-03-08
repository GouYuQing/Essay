# TypeScript

## 1.基础类型

### （1）布尔

```js
let isDone: boolean = false;
```

### （2）数字:所有都是浮点数，类型是number

```js
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

### （3）字符串定义

```js
let name: string = "bob";
```

模板字符串${变量名}

### （4）数组定义

```js
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

### （5）元祖Tuple:允许各元素类型不一样

```js
let x: [string, number];
x = ['hello', 10]; // OK
```

### （6）枚举：enum

```js
enum Color {Red, Green, Blue}
let c: Color = Color.Green;


//相比简单对象定义的key-value，只能通过key去访问value，不能通过value访问key。但是在enum当中，正反都可以当做key来用

enum HttpCode {
    /** 成功 */
    '200_OK' = 200,
    /** 已生成了新的资源 */
    '201_Created' = 201,
    /** 请求稍后会被处理 */
    '202_Accepted' = 202,
    /** 资源已经不存在 */
    '204_NoContent' = 204,
    /** 被请求的资源有一系列可供选择的回馈信息 */
    '300_MultipleChoices' = 300,
    /** 永久性转移 */
    '301_MovedPermanently' = 301,
    /** 暂时性转移 */
    '302_MoveTemporarily' = 302,
}

HttpCode['200_OK']
HttpCode[200]
```

### （7）Any类型：还不清楚变量的类型，不希望检查器对值进行检查

#### 2.语法简介

```js
// 'xxx: number' 表示声明一个number类型
const num: number = 123

// 声明一个函数的参数类型(number以及any)和返回值(void)
function fn (arg1: number, arg2: any): void {
    // todo
}
fn(num, [1,2,3,4])

// 声明一个接口
interface IPerson {
    name: string // IPerson需要包含一个name属性，类型是string
    age: number // IPerson需要包含一个age属性，类型是number
    family: string[] // IPerson需要包含一个family属性，类型是数组，数组里面都是string类型的数据
    sex?: '男' | '女' // IPerson可选一个sex属性，值为'男'或者'女'或者undefined
}
// 使用IPerson接口定义一个对象，如果对象不符合IPerson的定义，编译器会飘红报错
const person: IPerson = {
    name: '小王',
    age: 12,
    family: ['爹', '娘'],
}

// type类似interface，以下写法等同用interface声明IPerson
type IPerson2 = {
    name: string
    age: number
    family: string[]
    sex?: '男' | '女'
}
// 因此可以直接定义过来
const person2: IPerson2 = person
```

然后进行编译，去掉ts所有的代码，转换成js

```js
// 'xxx: number' 表示声明一个number类型
var num = 123;
// 声明一个函数的参数类型(number以及any)和返回值(void)
function fn(arg1, arg2) {
    // todo
}
fn(num, [1, 2, 3, 4]);
// 使用IPerson接口定义一个对象，如果对象不符合IPerson的定义，编译器会飘红报错
var person = {
    name: '小王',
    age: 12,
    family: ['爹', '娘'],
};
var person2 = person;
```

#### 3.应用场景

代码容错处理：使用自己写的方法时这里漏了一个参数，那里传错了参数类型。

#### 4.优点和不足

优点：

- 清晰的函数参数/接口属性，增加了代码可读性和可维护性
- 静态检查
- 生成API文档
- 配合现代编辑器，各种提示
- 活跃的社区

不足：和某些库结合的不是很完美(没错，说的就是vue 2.x)