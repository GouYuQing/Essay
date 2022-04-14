# TypeScript

## 1.什么是TS

类型错误，解决办法，是js的静态代码检查器，是JS的超集。

```js
let user={
    name:"hhh",
    age:21
}
user.location;//js会输出undefined ts会产生未定义错误+
```

ts可以检查类型错误、语法错误、逻辑错误、拼写错误

## 2.基础类型

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

枚举类型和数字类型兼容，但是不同枚举类型之间不兼容

```js
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };
let status = Status.Ready;
status = Color.Green;  // Error
```

### （7）Any类型：还不清楚变量的类型，不希望检查器对值进行检查

```js
const flag: any = document.getElementById("root");
```

### （8）UnKnown类型

所有类型都可以被归为 `unknown` 但`unknown`类型只能被赋值给 `any` 类型和 `unknown` 类型本身，而any啥都能分配和被分配

```js
let value: unknown;

value = true; // OK
value = 42; // OK
value = "Hello World"; // OK
value = []; // OK
value = {}; // OK

let value1: unknown = value; // OK
let value2: any = value; // OK
let value3: boolean = value; // Error
let value4: number = value; // Error
let value5: string = value; // Error
let value6: object = value; // Error
```

### （9）null和undefined

```js
let u: undefined = undefined;
let n: null = null;
```

### （10）void

表示没有任何类型

```js
function hello(name: string):void{}
```

### （11）Symbol

Symbols是不可改变且唯一的

```js
let sym2 = Symbol("key");
let sym3 = Symbol("key");
sym2 === sym3; // false, symbols是唯一的
```

Symbol被用作对象属性的键

```js
let sym = Symbol();
let obj = {
    [sym]: "value"
};
console.log(obj[sym]); // "value"
```

Symbols也可以与计算出的属性名声明相结合来声明对象的属性和类成员。

```js
const getClassNameSymbol = Symbol();
class C {
    [getClassNameSymbol](){
       return "C";
    }
}
let c = new C();
let className = c[getClassNameSymbol](); // "C"
```

## 3.其他类型

### （1）函数

```js
function greet(name: string):void {
    console.log("hello" + name)
}
```

函数兼容性

```js
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // OK
x = y; // Error
```

### （2）对象类型

```js
function print(pt: {name: string,age: number}){
    console.log(pt.name + "" + pt.age)
}
```

### （3）联合类型

```js
function printID(id:number|string){}
```

### （4）never类型

`never` 一般表示用户无法达到的类型 例如`never` 类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型

```js
function neverReach(): never {
  throw new Error("an error");
}
```

never 和 void 的区别 void 可以被赋值为 null 和 undefined 的类型。 never 则是一个不包含值的类型。 拥有 void 返回值类型的函数能正常运行。拥有 never 返回值类型的函数无法正常返回，无法终止，或会抛出异常。

never进行完整性检查：

```js
function assertNever(x: never): never {
    throw new Error("Unexpected object: " + x);
}
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
        default: return assertNever(s); // error here if there are missing cases
    }
}
```



### （5）object、Object和{}类型

**object** 类型用于表示非原始类型

```ts
let objectCase: object;
objectCase = 1; // error
objectCase = "a"; // error
objectCase = true; // error
objectCase = null; // error
objectCase = undefined; // error
objectCase = {}; // ok
复制代码
```

**大 Object** 代表所有拥有 toString、hasOwnProperty 方法的类型 所以所有原始类型、非原始类型都可以赋给 Object(严格模式下 `null` 和 `undefined` 不可以)

```ts
let ObjectCase: Object;
ObjectCase = 1; // ok
ObjectCase = "a"; // ok
ObjectCase = true; // ok
ObjectCase = null; // error
ObjectCase = undefined; // error
ObjectCase = {}; // ok
复制代码
```

**{}** 空对象类型和大 Object 一样 也是表示原始类型和非原始类型的集合

```ts
let simpleCase: {};
simpleCase = 1; // ok
simpleCase = "a"; // ok
simpleCase = true; // ok
simpleCase = null; // error
simpleCase = undefined; // error
simpleCase = {}; // ok
```



## 4.类型断言

你会比TypeScript更了解某个值的详细信息，你清楚地知道一个实体具有比它现有类型更确切的类型。告诉编译器知道这个类型通过断言的类型去通过编译

```js
// as 语法 理解为视为 把someValue看成一个string
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

## 5.类型别名

```js
type Point = {// type定义一个类型别名
    x: number;
    y: number
}
function printCoord（pt:Point）{}//使用

type ID = number | string;
function printID(id: ID){}
//type定义类型 type 类型别名：类型
```

## 6.interface

接口既可以在面向对象编程中表示为行为的抽象，也可以用来描述对象的形状

```js
//接口是定义对象类型的另外一种方式,通过关键词interface来定义
interface Point{
    x: number;
    y: number;
}
function printCoord(pt: Point){
    console.log(pt.x,pt.y)
}

//可选属性
interface SquareConfig {
  color?: string;
  width?: number;
}
// 只读属性 没办法修改
interface Point {
    readonly x: number;
    readonly y: number;
}
```

### （1）行为的抽象

一个类可以实现多个接口，一个接口也可以被多个类实现，implements实现接口

```js
//接口可以在面向对象编程中表示为行为的抽象
interface Speakable {
  speak(): void;
}
interface Eatable {
  eat(): void;
}
//一个类可以实现多个接口
class Person implements Speakable, Eatable {
  speak() {
    console.log("Person说话");
  }
  //   eat() {} //需要实现的接口包含eat方法 不实现会报错
}
```

### （2）定义任意属性

```js
interface Person {
  id: number;
  name: string;
  [propName: string]: any;
}

let p1 = {
  id: 1,
  name: "hello",
  age: 10,
};
//必须要有 id 和 name 这两个字段 然后还可以新加其余的未知字段
```

### （3）接口的继承

```js
interface Speakable {
  speak(): void;
}
interface SpeakChinese extends Speakable {
  speakChinese(): void;
}
class Person implements SpeakChinese {
  speak() {
    console.log("Person");
  }
  speakChinese() {
    console.log("speakChinese");
  }
}
```

### （4）函数类型的接口

```js
interface discount {
  (price: number): number;
}
let cost: discount = function (price: number): number {
  return price * 0.8;
};

```

### （5）型别名和接口的区别

#### a.扩展不同，接口扩展使用extends实现，类型别名通过交叉类型&实现

```js
// 接口扩展接口
interface PointX {
  x: number;
}

interface Point extends PointX {
  y: number;
}

// 类型别名扩展类型别名
type PointX = {
  x: number;
};

type Point = PointX & {
  y: number;
};

// 接口扩展类型别名
type PointX = {
  x: number;
};
interface Point extends PointX {
  y: number;
}

// 类型别名扩展接口
interface PointX {
  x: number;
}
type Point = PointX & {
  y: number;
};
```

#### b.重复定义

接口重复定义会合并成一个接口，类型别名不可以重复定义

```js
interface Point {
  x: number;
}
interface Point {
  y: number;
}
const point: Point = { x: 1, y: 2 };//直接合并成两个元素的接口

type Point = {
  x: number;
}
type Point = {
  y: number;
}//已经开始报错，不能定义一样的
```

### （6）接口vs类型别名

接口和类型别名的细微差别

接口创建了一个新的名字，可以在其它任何地方使用。 类型别名并不创建新名字—比如，错误信息就不会使用别名。 在下面的示例代码里，在编译器中将鼠标悬停在 `interfaced`上，显示它返回的是 `Interface`，但悬停在 `aliased`上时，显示的却是对象字面量类型。

```js
type Alias = { num: number }
interface Interface {
    num: number;
}
declare function aliased(arg: Alias): Alias;
declare function interfaced(arg: Interface): Interface;
```

类型别名不能被 `extends`和 `implements`（自己也不能 `extends`和 `implements`其它类型）。 因为 [软件中的对象应该对于扩展是开放的，但是对于修改是封闭的](https://en.wikipedia.org/wiki/Open/closed_principle)，你应该尽量去使用接口代替类型别名。

## 7.类

使用Class定义一个类

```js
class Person {
  name!: string; //如果初始属性没赋值就需要加上!
  constructor(_name: string) {
    this.name = _name;
  }
//上述两句也可以写成
/*
* constructor(public name:string){}
* */
  getName(): void {
    console.log(this.name);
  }
}
let p1 = new Person("hello");
p1.getName();
```

### （1）存取器

```js
class User {
  myname: string;
  constructor(myname: string) {
    this.myname = myname;
  }
  get name() {
    return this.myname;
  }
  set name(value) {
    this.myname = value;
  }
}

let user = new User("hello");
user.name = "world";
console.log(user.name);
//翻译成 es5 的代码 原理很简单 就是使用了 Object.defineProperty 在类的原型上面拦截了属性对应的 get 和 set 方法
var User = /** @class */ (function () {
  function User(myname) {
    this.myname = myname;
  }
  Object.defineProperty(User.prototype, "name", {
    get: function () {
      return this.myname;
    },
    set: function (value) {
      this.myname = value;
    },
    enumerable: false,
    configurable: true,
  });
  return User;
})();
var user = new User("hello");
user.name = "world";
console.log(user.name);
```

### (2）继承：使用extends关键字

```js
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {//Shake和Horse都是派生类，子类
    constructor(name: string) { super(name); }//调用super，继承父类属性和方法
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

### （3）public、private、protected

**public** 类里面/子类 /其它任何地方外边都可以访问 ，ts一般默认public

**protected** 类里面 /子类 都可以访问,其它任何地方不能访问 

**private** 类里面可以访问，子类和其它任何地方都不可以访问

```js
class Parent {
  public name: string;
  protected age: number;
  private car: number;
  constructor(name: string, age: number, car: number) {//构造函数
    this.name = name;
    this.age = age;
    this.car = car;
  }
  getName(): string {
    return this.name;
  }
  setName(name: string): void {
    this.name = name;
  }
}
class Child extends Parent {
  constructor(name: string, age: number, car: number) {
    super(name, age, car);
  }
  desc() {
    console.log(`${this.name} ${this.age} ${this.car}`); //name可以访问、age可以访问、car访问不到会报错
  }
}

let child = new Child("hello", 10, 1000);
console.log(child.name);
console.log(child.age); //age访问不到 会报错
console.log(child.car); //car访问不到 会报错
```

### （4）readonly只读属性

`readonly`关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

```js
class Animal {
  public readonly name: string;
  constructor(name: string) {
    this.name = name;
  }
  changeName(name: string) {
    this.name = name; //这个ts是报错的,因为只读不能修改
  }
}

let a = new Animal("hello");
a.name = "hello world";//error，只读不能修改
```

### （5）静态属性

静态属性和方法是直接定义在类的本身上的，所以只能通过直接调用类的方法和属性来访问

```js
class Parent {
  static mainName = "Parent";
  static getmainName() {
    console.log(this); //注意静态方法里面的this指向的是类本身 而不是类的实例对象 所以静态方法里面只能访问类的静态属性和方法
    return this.mainName;
  }
  public name: string;
  constructor(name: string) {//构造函数
    this.name = name;
  }
}
console.log(Parent.mainName);
console.log(Parent.getmainName());
```

### （6）抽象类和抽象方法

抽象类，无法被实例化，只能被继承并且无法创建抽象类的实例 ,子类可以对抽象类进行不同的实现

抽象方法只能出现在抽象类中并且抽象方法不能在抽象类中被具体实现，只能在抽象类的子类中实现（必须要实现）

使用场景：我们一般用抽象类和抽象方法抽离出事物的共性 以后所有继承的子类必须按照规范去实现自己的具体逻辑 这样可以增加代码的可维护性和复用性

```js
abstract class Animal {
  name!: string;
  abstract speak(): void;
}
class Cat extends Animal {
  speak() {//抽象方法必须实现
    console.log("喵喵喵");
  }
}
let animal = new Animal(); //直接报错 无法创建抽象类的实例
let cat = new Cat();
cat.speak();
```

### （7）重写重载区别

**重写**是指子类重写继承自父类中的方法 **重载**是指为同一个函数提供多个类型定义

## 8.交叉类型

交叉类型是将多个类型合并为一个类型。通过 & 运算符可以将现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性

```js
type Flag1 = { x: number };
type Flag2 = Flag1 & { y: string };

let flag3: Flag2 = {
  x: 1,
  y: "hello",
};
```

## 9.泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性

我们预先不知道会传入什么类型 但是我们希望不管我们传入什么类型 我们的返回的数据的类型应该和参数保持一致 那么这时候 泛型就登场了

```js
function identity<T>(arg: T): T {
    return arg;
}
```

### （1）多个类型参数

```js
//如果需要有多个未知的类型占位 那么我们可以定义任何的字母来表示不同的类型参数
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]];
}
swap([7, "seven"]); // ['seven', 7]
```

### （2）泛型约束

```js
function loggingIdentity<T>(arg: T): T {
  console.log(arg.length);
  return arg;
}
// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'T'.
//初始不知道传入的数据是否有属性length，所以编译的时候报错了
```

所以可以进行约束，只允许传入含有length属性的变量

```js
interface Lengthwise {
  length: number;
}
//泛型里面使用extends代表泛型约束 和继承不同了
function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

### （3）泛型接口

```js
interface Cart<T> {
  list: T[];
}
let cart: Cart<{ name: string; price: number }> = {
  list: [{ name: "hello", price: 10 }],
};
console.log(cart.list[0].name, cart.list[0].price);
```

### （4）泛型类

```js
//demo1
class MyArray<T> {
  private list: T[] = [];
  add(value: T) {
    this.list.push(value);
  }
  getMax(): T {
    let result = this.list[0];
    for (let i = 0; i < this.list.length; i++) {
      if (this.list[i] > result) {
        result = this.list[i];
      }
    }
    return result;
  }
}
let arr = new MyArray();
arr.add(1);
arr.add(2);
arr.add(3);
let ret = arr.getMax();
console.log(ret);
//demo2
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}
//可以是number也可以是string
let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };

let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function(x, y) { return x + y; };
console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

### （5）泛型类型

```js
function indentity<T>(arg: T):T{
    return arg;
}
let myIdentity: <T>(arg : T) => T = identity;
```

### （6）泛型类型别名

```js
type Cart<T> = { list: T[] } | T[];
let c1: Cart<string> = { list: ["1"] };
let c2: Cart<number> = [1];
```

### （7）泛型参数的默认类型

```js
function createArray<T = string>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
```

## 10.预定义的有条件类型

TypeScript 2.8在`lib.d.ts`里增加了一些预定义的有条件类型：

- `Exclude<T, U>` -- 从`T`中去除`T,U`共有的类型
- `Extract<T, U>` -- 提取`T`中`T`,`U`共有类型。
- `NonNullable<T>` -- 从`T`中剔除`null`和`undefined`。
- `ReturnType<T>` -- 获取函数返回值类型。
- `InstanceType<T>` -- 获取构造函数类型的实例类型

```js
type T00 = Exclude<"a" | "b" | "c" | "d", "a" | "c" | "f">;  // "b" | "d"
type T01 = Extract<"a" | "b" | "c" | "d", "a" | "c" | "f">;  // "a" | "c"

type T02 = Exclude<string | number | (() => void), Function>;  // string | number
type T03 = Extract<string | number | (() => void), Function>;  // () => void

type T04 = NonNullable<string | number | undefined>;  // string | number
type T05 = NonNullable<(() => string) | string[] | null | undefined>;  // (() => string) | string[]

function f1(s: string) {
    return { a: 1, b: s };
}

class C {
    x = 0;
    y = 0;
}

type T10 = ReturnType<() => string>;  // string
type T11 = ReturnType<(s: string) => void>;  // void
type T12 = ReturnType<(<T>() => T)>;  // {}
type T13 = ReturnType<(<T extends U, U extends number[]>() => T)>;  // number[]
type T14 = ReturnType<typeof f1>;  // { a: number, b: string }
type T15 = ReturnType<any>;  // any
type T16 = ReturnType<never>;  // any
type T17 = ReturnType<string>;  // Error
type T18 = ReturnType<Function>;  // Error

type T20 = InstanceType<typeof C>;  // C
type T21 = InstanceType<any>;  // any
type T22 = InstanceType<never>;  // any
type T23 = InstanceType<string>;  // Error
type T24 = InstanceType<Function>;  // Error
```

## 11.infer关键字（！！）

Infer 关键字用于条件中的类型推导。

Typescript 官网也拿 `ReturnType` 这一经典例子说明它的作用：

```js
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
```

理解为：如果 `T` 继承了 `extends (...args: any[]) => any` 类型，则返回类型 `R`，否则返回 `any`。其中 `R` 是什么呢？`R` 被定义在 `extends (...args: any[]) => infer R` 中，即 R 是从传入参数类型中推导出来的。

深入理解：

### （1）从需求角度理解infer

需求：实现一个函数，接收一个数组，返回第一项

无法使用泛型,因为泛型只能返回整体T[0]没法拿到第一项，因为T如果是对象，想返回一个key的返回值类型，就没法办到

```js
function xxx<T>(...args: T[]): T[0]
```

### （2）设计角度理解infer

泛型是不可拆分的整体，为了允**许描述任意类型的位置**，使用infer,传入参数可能是`{label:xxx}`，也可能是`（）=>void`结构, 所以考虑到想要拿到 `{label: infer R}`，首先参数必须具备 `{label: xxx}` 的结构，所以正好可以将 `infer` 与条件判断 `T extends ? A : B` 结合起来用

```js
type GetLabelTypeFromObject<T> = T extends { label: infer R } ? R : never
type Result = GetLabelTypeFromObject<{ label: string }>;
// type Result = string
```

实现需求:

```js
function getForstParamsType<T> = T extends (...args: infer R) => any ?R[0] : never
```

### （3）例子理解

```js
//demo1
type ArrayElementType<T> = T extends (infer E)[] ? E : T;
// type of item1 is `number`
type item1 = ArrayElementType<number[]>;//满足结构返回number
// type of item1 is `{name: string}`
type item2 = ArrayElementType<{ name: string }>;//不满足结构，返回类型本身
```

```js
type Bar<T> = T extends { a: (x: infer U) => void; b: (x: infer U) => void }
  ? U : never
type T21 = Bar<{ a: (x: string) => void; b: (x: number) => void }>; // string & number
```

## 12.装饰器

用于支持标注修改类及其成员,能使用@符号将其附加到类声明，方法，访问符，属性，参数上，

装饰器使用 `@expression`这种形式，`expression`求值后必须为一个函数，它会在运行时被调用，被装饰的声明信息做为参数传入。

启用装饰器tsconfig.json配置

```js
{
    "compilerOptions": {
        "target": "ES5",
        "experimentalDecorators": true
    }
}
```

### （1）装饰器组合

```js
@f
@g
x
```

当多个装饰器应用于一个声明上，它们求值方式与[复合函数](http://en.wikipedia.org/wiki/Function_composition)相似。在这个模型下，当复合*f*和*g*时，复合的结果(*f* ∘ *g*)(*x*)等同于*f*(*g*(*x*))。

```js
function f() {
    console.log("f(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("f(): called");
    }
}

function g() {
    console.log("g(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("g(): called");
    }
}

class C {
    @f()
    @g()
    method() {}
}
/*
f(): evaluated
g(): evaluated
g(): called
f(): called
*/
//调用顺序相当于f(g（x）),
//1.装饰器表达式求值，由上至下2.求值的结果会被当成函数由下至上调用返回
```

### （2）类装饰器（！！）

在类声明之前被声明，用于监视修改替换类定义

类装饰器表达式会在运行时当作函数被调用，类的构造函数作为其唯一的参数

```js
@sealed
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

//@sealed装饰器
function sealed(constructor: Function) {
    Object.seal(constructor);
    Object.seal(constructor.prototype);
}
```

```js
//重载构造函数demo
function classDecorator<T extends {new(...args:any[]):{}}>(constructor:T) {
    return class extends constructor {
        newProperty = "new property";
        hello = "override";
    }
}

@classDecorator
class Greeter {
    property = "property";
    hello: string;
    constructor(m: string) {
        this.hello = m;
    }
}

console.log(new Greeter("world"));
/*
class_1 {
  property: 'property',
  hello: 'override',
  newProperty: 'new property'
}
*/
```

### （3）方法装饰器（！！）

声明在一个方法的声明之前，用来监视修改替换方法定义

方法装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。
3. 成员的*属性描述符*。

> 注意  如果代码输出目标版本小于`ES5`，*属性描述符*将会是`undefined`

```js
//该demo修改了属性描述符的enumerable属性
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }

    @enumerable(false)
    greet() {
        return "Hello, " + this.greeting;
    }
}
function enumerable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.enumerable = value;
    };
}
```

### （3）访问器装饰器

声明在一个访问器的声明之前

传参同方法装饰器

```js
class Point {
    private _x: number;
    private _y: number;
    constructor(x: number, y: number) {
        this._x = x;
        this._y = y;
    }

    @configurable(false)
    get x() { return this._x; }

    @configurable(false)
    get y() { return this._y; }
}

function configurable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.configurable = value;
    };
}
```

### （4）属性装饰器

*属性装饰器*声明在一个属性声明之前

属性装饰器表达式会在运行时当作函数被调用，传入下列2个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 成员的名字。

```js
//记录greeting这个属性的元数据
class Greeter {
    @format("Hello, %s")
    greeting: string;

    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        let formatString = getFormat(this, "greeting");
        return formatString.replace("%s", this.greeting);
    }
}

import "reflect-metadata";
const formatMetadataKey = Symbol("format");
function format(formatString: string) {
    return Reflect.metadata(formatMetadataKey, formatString);
}
function getFormat(target: any, propertyKey: string) {
    return Reflect.getMetadata(formatMetadataKey, target, propertyKey);
}
```

## 13.常用技巧

### （1）keyof关键词

`keyof` 可以用来取得一个对象接口的所有 key 值

```js
interface Person {
  name: string;
  age: number;
  gender: "male" | "female";
}
//type PersonKey = 'name'|'age'|'gender';
type PersonKey = keyof Person;

function getValueByKey(p: Person, key: PersonKey) {
  return p[key];
}
let val = getValueByKey({ name: "hello", age: 10, gender: "male" }, "name");
console.log(val);
```

```js
function getProperty<T, K extends keyof T>(o: T, name: K): T[K] {
    return o[name]; // o[name] is of type T[K]
}
```



### （2）索引操作访问符[]

```js
interface Person {
  name: string;
  age: number;
}
//使用 [] 操作符可以进行索引访问
type x = Person["name"]; // x is string
```

### （3）映射类型 in

```js
interface Person {
  name: string;
  age: number;
  gender: "male" | "female";
}
//批量把一个接口中的属性都变成可选的
type PartPerson = {
  [Key in keyof Person]?: Person[Key];
};

let p1: PartPerson = {};
```

### （4）`for of`和`for in`

`for of`会遍历可迭代对象

区别1：`for..of`和`for..in`均可迭代一个列表；但是用于迭代的值却不同，`for..in`迭代的是对象的 *键* 的列表，而`for..of`则迭代对象的键对应的值

```js
let list = [4, 5, 6];
for (let i in list) {
	console.log(i); // "0", "1", "2",
}
for (let i of list) {
	console.log(i); // "4", "5", "6"
}
```

区别2：`for..in`可以操作任何对象；它提供了查看对象属性的一种方法。 但是 `for..of`关注于迭代对象的值

```js
let pets = new Set(["Cat", "Dog", "Hamster"]);
pets["species"] = "mammals";
for (let pet in pets) {
    console.log(pet); // "species"
}
for (let pet of pets) {
    console.log(pet); // "Cat", "Dog", "Hamster"
}
```

## 14.compilerOptions 选项

```js
{
  "compilerOptions": {

    /* 基本选项 */
    "target": "es5",                       // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES6'/'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'
    "module": "commonjs",                  // 指定使用模块: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
    "lib": [],                             // 指定要包含在编译中的库文件
    "allowJs": true,                       // 允许编译 javascript 文件
    "checkJs": true,                       // 报告 javascript 文件中的错误
    "jsx": "preserve",                     // 指定 jsx 代码的生成: 'preserve', 'react-native', or 'react'
    "declaration": true,                   // 生成相应的 '.d.ts' 文件
    "sourceMap": true,                     // 生成相应的 '.map' 文件
    "outFile": "./",                       // 将输出文件合并为一个文件
    "outDir": "./",                        // 指定输出目录
    "rootDir": "./",                       // 用来控制输出目录结构 --outDir.
    "removeComments": true,                // 删除编译后的所有的注释
    "noEmit": true,                        // 不生成输出文件
    "importHelpers": true,                 // 从 tslib 导入辅助工具函数
    "isolatedModules": true,               // 将每个文件做为单独的模块 （与 'ts.transpileModule' 类似）.

    /* 严格的类型检查选项 */
    "strict": true,                        // 启用所有严格类型检查选项
    "noImplicitAny": true,                 // 在表达式和声明上有隐含的 any类型时报错
    "strictNullChecks": true,              // 启用严格的 null 检查
    "noImplicitThis": true,                // 当 this 表达式值为 any 类型的时候，生成一个错误
    "alwaysStrict": true,                  // 以严格模式检查每个模块，并在每个文件里加入 'use strict'

    /* 额外的检查 */
    "noUnusedLocals": true,                // 有未使用的变量时，抛出错误
    "noUnusedParameters": true,            // 有未使用的参数时，抛出错误
    "noImplicitReturns": true,             // 并不是所有函数里的代码都有返回值时，抛出错误
    "noFallthroughCasesInSwitch": true,    // 报告 switch 语句的 fallthrough 错误。（即，不允许 switch 的 case 语句贯穿）

    /* 模块解析选项 */
    "moduleResolution": "node",            // 选择模块解析策略： 'node' (Node.js) or 'classic' (TypeScript pre-1.6)
    "baseUrl": "./",                       // 用于解析非相对模块名称的基目录
    "paths": {},                           // 模块名到基于 baseUrl 的路径映射的列表
    "rootDirs": [],                        // 根文件夹列表，其组合内容表示项目运行时的结构内容
    "typeRoots": [],                       // 包含类型声明的文件列表
    "types": [],                           // 需要包含的类型声明文件名列表
    "allowSyntheticDefaultImports": true,  // 允许从没有设置默认导出的模块中默认导入。

    /* Source Map Options */
    "sourceRoot": "./",                    // 指定调试器应该找到 TypeScript 文件而不是源文件的位置
    "mapRoot": "./",                       // 指定调试器应该找到映射文件而不是生成文件的位置
    "inlineSourceMap": true,               // 生成单个 soucemaps 文件，而不是将 sourcemaps 生成不同的文件
    "inlineSources": true,                 // 将代码与 sourcemaps 生成到一个文件中，要求同时设置了 --inlineSourceMap 或 --sourceMap 属性

    /* 其他选项 */
    "experimentalDecorators": true,        // 启用装饰器
    "emitDecoratorMetadata": true          // 为装饰器提供元数据的支持
  }
}
```

## 15.应用场景

代码容错处理：使用自己写的方法时这里漏了一个参数，那里传错了参数类型。

## 16.优点和不足

优点：

- 清晰的函数参数/接口属性，增加了代码可读性和可维护性
- 静态检查
- 生成API文档
- 配合现代编辑器，各种提示
- 活跃的社区

不足：和某些库结合的不是很完美(vue 2.x)