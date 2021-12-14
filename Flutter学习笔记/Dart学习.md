# Dart

https://www.w3cschool.cn/nxvsy/nxvsy-58fq35q6.html

Dart由谷歌开发的编程语言，可以被用于web端，服务器，移动端等

## 1.变量

dart是可以推断类型的，用var，如果固定了类型就会有类型校验

变量是一个引用，Dart中万物皆对象，变量存储的都是对象的引用，或者说他们都指向对象

### （1）变量声明（2种）

不指定类型，使用var

```
var name = 'Bob';
```

明确指定类型（Optional types）

```
String name = 'Bob';
```

因为有类型推导，所以两种实现效果一样，官方推荐在函数内的本地变量尽量使用var声明。

变量类型不明确的情况下，可以使用dynamic关键字

```
dynamic name = 'Bob';
```

### （2）默认值

未初始化的变量默认值null，数字类型也是null，因为dart中万物皆对象

### （3）Final 和Const

不会被修改的变量，用final和const

Final 变量的值只能被设置一次； Const 变量在编译时就已经固定 (Const 变量 是隐式 Final 的类型) 。最高级 final 变量或类变量在第一次使用时被初始化

```dart
// 创建和设置一个 Final 变量
final name = 'Bob'; // Without a type annotation
final String nickname = 'Bobby';
name = 'Alice'; // Error: 一个 final 变量只能被设置一次。
// 创建设置Const变量 static const可以作为类级别被引用
const bar = 1000000; // 压力单位 (dynes/cm2)
const double atm = 1.01325 * bar; // 标准气压
// Const创建常量值
var foo = const [];
```

## 2.内建类型

Number、String、Boolean、List(也被称为 Array)、Map、Set、Rune (用于在字符串中表示 Unicode 字符)、Symbol

### （1）Number

int:整数值不大于64位

double:64位（双精度）浮点数，依据 IEEE 754 标准。

从dart2.1开始，必要时候int字面量会自动转换成double

num 类型包括基本运算 +， -， /， 和 *， 以及 abs()， ceil()， 和 floor()， 等函数方法,int 特有的传统按位运算操作，移位（<<， >>），按位与（&）以及 按位或（|）

```dart
// String -> int
var one = int.parse('1');
print(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
print(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
print(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
print(piAsString == '3.14');

print((3 << 1) == 6); // 0011 << 1 == 0110
```

### （2）String

字符串通过单引号或者双引号创建，字符串可以通过 ${expression} 的方式内嵌表达式

 == 运算符用来测试两个对象是否相等，\+ 运算符来把多个字符串连接为一个

使用连续三个单引号或者三个双引号实现多行字符串对象的创建

```dart
var s2 = """This is also a
multi-line string.""";
```

### （3）Boolean 

 Dart 只有字面量 true and false 是布尔类型

```dart
// 检查空字符串。
var fullName = '';
print(fullName.isEmpty);

// 检查 0 值。
var hitPoints = 0;
print(hitPoints <= 0);

// 检查 null 值。
var unicorn;
print(unicorn == null);

// 检查 NaN 。
var iMeantToDoThis = 0 / 0;
print(iMeantToDoThis.isNaN);
```

### （4）List

非常像 JavaScript 中的 array 字面量

```dart
var list = [1, 2, 3];
print(list.length == 3);
var arr = <String>["hello","world"];//指定类型
//可以通过add增加数据
arr.add(4); //[1,2,3,4]
// 可以通过filled创建
var arr2 = List.filled(2,"");//打印[,]
//直接修改使用下标
arr2[0]="hello";
arr2[1] = "world";
arr2.add("bububu");//报错 因为上面指定了长度为2，也没法修改集合长度
// 通过中括号[]创建可以修改数组，可以修改长度，但是通过filled不能
```

常见方法属性

```dart
List arr = [1,2,3];
print(arr.length);
print(arr.isEmpty);
print(arr.isNotEmpty);
print(arr.reversed);//对列表倒序排序
var newMyList = arr.reversed.toList();
print(newMyList);
arr.add(4);//增加元素
arr.addAll([5,6,7]);//拼接增加数组
print(arr.indexOf(4));//查找 传入具体值
arr.remove(7);//删除 传入具体值
arr.removeAt(1)://删除 传入索引值
arr.fillRange(1,2,9);//arr.fillRange(start,end,value);
arr.insert(1,8)//arr的1下标之前加8 插入一个
arr.insertAll(1,[9,5,6]);//arr的1下标之前加数组 插入多个
var str = arr.join("-");//转换成字符串类型，用-链接
str.split('-');//字符串按照-分隔成数组list
```

### （5）Set

Set 是一个元素唯一且无序的集合,不能添加重复的数据

```dart
var names = <String>{};
// Set<String> names = {}; // 这样也是可以的。
// var names = {}; // 这样会创建一个 Map ，而不是 Set 。
names.add("hello");
names.addAll(arr1);
print(names.length == 5);
```

常用方法

```dart
var s  = new Set();
s.add(1);
s.add(2);
print(s);
print(s.toList());// Set转换成List
```

### （6）Map

 Map 是用来关联 keys 和 values 的对象。 keys 和 values 可以是任何类型的对象。在一个 Map 对象中一个 key 只能出现一次，values可以出现多次

```dart
// 创建
var gifts = Map();// dart里面 new关键字是可选的
var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
// 可以根据key或得values，要使用中括号['key'],不能和js一样使用.
print(gifts['fifth'] == null); //Map 中不包含所要查找的 key，那么 Map 返回 null
```

### （7）Rune

 Rune 用来表示字符串中的 UTF-32 编码字符

### （8）Symbol

一个 Symbol 对象表示 Dart 程序中声明的运算符或者标识符

通过字面量 Symbol ，也就是标识符前面添加一个 # 号，来获取标识符的 Symbol 。

```dart
#radix
#bar
```

### （9）Number和String类型转换

```dart
//Number转换成String,toString()
var num = 123;
var str = num.toString();
print(str is String);

// String转换成Number,parse()
String str = '123';
var myNum = int.parse(str);// int类型
String str = '123.1';
var myNum = double.parse(str);// double

// 其他类型转换成boolean
var str = '';
if(str.isEmpty){}
```

## 3.可选参数

### （1）命名可选参数

```dart
enableFlags(bold: true, hidden: false);
void enableFlags({bool bold, bool hidden}) {...}
const Scrollbar({Key key, @required Widget child})// 必传，不传报错
```

### （2）位置可选参数

将参数放到 [] 中来标记参数是可选的

```dart
String say(String from, String msg, [String device]){}
```

### （3）默认参数

使用 = 来定义可选参数的默认值

```dart
void enableFlags({bool bold = false, bool hidden = false}) {...}
String say(String from, String msg,[String device = 'carrier pigeon', String mood]){}// 位置可选参数设置默认参数
```

## 4.函数基础

### （1）main函数

任何应用都必须有一个顶级 main() 函数，作为应用服务的入口,参数为一个可选的 List<String>

```dart
void main(){}
```

函数式一等对象，可以作为参数传递，赋值给变量

### （2）匿名函数

没有名字的函数，也被称为 lambda 或者 closure， 匿名函数可以赋值到一个变量中

```dart
var printNum = (int n)=>{
	print(n+2);
}
```

### （3）箭头函数

如果函数只有一条语句，还可以使用箭头函数

```dart
item.forach(item=>{...})// 箭头函数 只能写一行
item.forEach((value) {
    print(value);
})
```

### （4）自执行方法

```dart
((){
	print("我是自执行方法");
})
((int n){
    print(n);
	print("我是自执行方法");
})
```

### （5）词法作用域

Dart 是一门词法作用域的编程语言，就意味着变量的作用域是固定的，下级作用域可以访问上级的作用域，直到顶级作用域

### （6）词法闭包

闭包 即一个函数对象，即使函数对象的调用在它原始作用域之外， 依然能够访问在它词法作用域内的变量

函数可以封闭定义到它作用域内的变量

函数也有作用域，不在作用域内无法调用

### （7）测试函数是否相等

```dart
void foo() {} // 顶级函数

class A {
  static void bar() {} // 静态方法
  void baz() {} // 示例方法
}

void main() {
  var x;

  // 比较顶级函数。
  x = foo;
  print(foo == x);

  // 比较静态方法。
  x = A.bar;
  print(A.bar == x);

  // 比较实例方法。
  var v = A(); // A的1号实例
  var w = A(); // A的2号实例
  var y = w;
  x = w.baz;

  // 两个闭包引用的同一实例（2号）,
  // 所以它们相等。
  print(y.baz == x);

  // 两个闭包引用的非同一个实例，
  // 所以它们不相等。
  print(v.baz != w.baz);
}
```

### （6）返回值

所有函数都有返回值，没有的默认返回null

```dart
foo() {}
print(foo() == null);
```

## 5.运算符

### （1）算术运算符

```dart
print(2 + 3 == 5);
print(2 - 3 == -1);
print(2 * 3 == 6);
print(5 / 2 == 2.5); // 结果是双浮点型
print(5 ~/ 2 == 2); // 结果是整型,取整
print(5 % 2 == 1); // 余数

print('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');

// 自增自减
++i;
i++;
--i;
i--;
  
```

### （2）关系运算符

```dart
print(2 == 2);
print(2 != 3);
print(3 > 2);
print(2 < 3);
print(3 >= 3);
print(2 <= 3);
// 测试两个对象x和y是否表示相同的事物， 使用 == 运算符。 (在极少数情况下， 要确定两个对象是否完全相同，需要使用 identical() 函数
```

### （3）类型判断运算符

as,is,is!

is是继承，obj is Object 总是 true， 但是只有 obj 实现了 T 的接口时， obj is T 才是 true

as是将对象强制转换

```dart
str is String// 判断是不是String类型
str as String //强制转换成String类型
    
```

### （4）赋值运算符

```dart
// 将值赋值给变量a
a = value;
// 如果b为空时，将变量赋值给b，否则，b的值保持不变。
b ??= value;
```

| `=`  | `–=` | `/=`  | `%=`  | `>>=` | `^=` |
| ---- | ---- | ----- | ----- | ----- | ---- |
| `+=` | `*=` | `~/=` | `<<=` | `&=`  | `|=` |

### （5）逻辑运算符

非！，与&&，或||

### （6）按位和移位运算符

&,|,^,~,<<,>>

&&有短路功能，&必须满足所有

### （7）条件表达式

```dart
var visibility = isPublic ? 'public' : 'private';//赋值是根据布尔值， 考虑使用 ?:
String playerName(String name) => name ?? 'Guest'; //赋值是基于判定是否为 null
```

### （8）级联运算符

不是运算符 是dart的特殊语法

```dart
querySelector('#confirm') // 获取对象。
  ..text = 'Confirm' // 调用成员变量。
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
//等同于
var button = querySelector('#confirm');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));
```

```dart
Person p1 = new Person();
p1.name = "zhangsan";
p1.age = 20;
p1.printInfo();
//等同于
p1..name = "zhangsan"
  ..age = 20
  ..printInfo();
```

## 6.流程控制语句

```dart
if...else
for(int i = 1; i < 10; i ++){
    print(i);
}

while (!isDone()) {
  doSomething();
}

do {
  printLine();
} while (!atEndOfPage());
// do..while和while的区别，do...while不满足条件也会执行一次
break // 跳过循环，不再执行循环
continue //跳过当前循环，程序继续执行循环
switch (command) {
  case 'CLOSED':
    	executeClosed();
    break;
}
```

assert

如果 assert语句中的布尔条件为 false ,那么正常的程序执行流程会被中断

assert语句只在开发环境中有效， 在生产环境是无效的； Flutter 中的 print 只在 debug 模式 中有效。

```dart
assert(1 === 1)
```

## 7.异常

```dart
throw FormatException('Expected at least 1 section');//throw
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 一个特殊的异常
  buyMoreLlamas();
} on Exception catch (e) {
  // 其他任何异常
  print('Unknown exception: $e');
} catch (e) {
  // 没有指定的类型，处理所有异常
  print('Something really unknown: $e');
}// try catch
```

finally:不管是否抛出异常， finally 中的代码都会被执行

## 8.类

Dart 是一种基于类和 mixin 继承机制的面向对象的语言， 每个对象都是一个类的实例，所有的类都继承于 Object. 。

 基于 Mixin 继承意味着每个类（除 Object 外） 都只有一个超类， 一个类中的代码可以在其他多个继承类中重复使用。

使用.调用类的方法和变量

### （1）构造函数

```dart
class Point {
  num x, y;

  Point(num x, num y) {// 默认构造函数
    // 还有更好的方式来实现下面代码，敬请关注。
    this.x = x;
    this.y = y;
  }
}
// 变成
class Point {
  num x, y;

  // 在构造函数体执行前，
  // 语法糖已经设置了变量 x 和 y。
  Point(this.x, this.y);// 默认构造函数简写
}
```

没有构造函数的时候,dart会提供默认构造函数

构造函数不被继承，子类不会继承父类的构造函数。 子类不声明构造函数，那么它就只有默认构造函数 (匿名，没有参数) 。

```dart
// 重定向构造函数
class Point {
  num x, y;
  // 类的主构造函数
  Point(this.x, this.y);// 默认构造函数 只有一个
  // 命名构造函数 可以多个
  Point.now(){
      print("hehehe")
  }
  Point.setInfo(x,y){
       this.x = x;
       this.y = y;
    }
}
var p1 = new Point(1,2);//调用默认构造函数
var p1 = new Point.now();//调用命名构造函数
```

```dart
class Logger {
  final String name;
  bool mute = false;

  // _是把方法或者属性变成私有，私有方法属性只有当前类可以直接访问，实例只能访问共有的，间接去访问私有的属性和方法
  // _cache 是私有属性。
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {// 工厂构造函数 ，可能返回一个catche中的实例，或者可能返回一个子类的实例
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
// 工厂构造函数无法访问 this。
```

```dart
class Rect{
	int height;
    int width;
    Rect():height = 2,width = 10 {// 初始化实例变量
        
    }
    getArea() {
        return this.height*this.width;
    }
}
```

### （2）const

在 常量上下文 中， 构造函数或者字面量前的 const 可以省略

```dart
// 这里有很多的 const 关键字。
const pointAndLine = const {
  'point': const [const ImmutablePoint(0, 0)],
  'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
};
// 保留第一个 const ，由该 const 建立常量上下文。
const pointAndLine = {
  'point': [ImmutablePoint(0, 0)],
  'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
};
```

获取对象的类型： runtimeType 属性回返回一个 Type 对象。

```dart
print('The type of a is ${a.runtimeType}');
```

### （3）抽象方法

实例方法， getter， 和 setter 方法可以是抽象的， 只定义接口不进行实现,留给其他类去实现

```dart
abstract class Doer {
  // 定义实例变量和方法 ...

  void doSomething(); // 定义一个抽象方法。
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // 提供方法实现，所以这里的方法就不是抽象方法了...
  }
}
```

### （4）抽象类

使用 abstract 修饰符来定义 抽象类， 抽象类不能实例化,抽象类用来定义接口，抽象类被继承的话必须实现抽象方法

```dart
// 这个类被定义为抽象类，
// 所以不能被实例化。
abstract class AbstractContainer {
  // 定义构造行数，字段，方法...

  void updateChildren(); // 抽象方法。
}
class HaHa extends AbstractContainer{
    void updateChildren(){
        print("必须实现抽象方法")
    }
}
```

### （5）隐式接口

每个类都隐式的定义了一个接口，接口包含了该类所有的实例成员及其实现的接口。 如果要创建一个 A 类，A 要支持 B 类的 API ，但是不需要继承 B 的实现， 那么可以通过 A 实现 B 的接口

一个类可以通过 implements 关键字来实现一个或者多个接口， 并实现每个接口要求的 API。 

```dart
// person 类。 隐式接口里面包含了 greet() 方法声明。
class Person { // 建议使用抽象类去定义接口，更好实现被继承
  // 包含在接口里，但只在当前库中可见。
  final _name;

  // 不包含在接口里，因为这是一个构造函数。
  Person(this._name);

  // 包含在接口里。
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// person 接口的实现,使用implements实现
class Impostor implements Person {
  get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

实现多个接口

```dart
class Point implements Comparable, Location {...}
```

### （6）继承

使用 extends 关键字来创建子类， 使用 super 关键字来引用父类

子类继承父类可见的属性和方法，但是不会继承构造函数

子类可以定义自己的方法，复写父类的方法

```dart
class Person {
	String name;
	num age;
	Person(this.name,this.age);
    void printInfo(){}
}
class Web extends Person {
    String sex;
    Web(String name,num age,String sex):super(name, age){
        this.sex = sex;
    }//新增属性
    void printInfo(){
        print("去实现父类的方法");
    }
    run(){}//扩展自己的方法
}

```

```dart
class SmartTelevision extends Television {
  void turnOn() {
    super.turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ···
}
```

### （7）重写

```dart
class SmartTelevision extends Television {
  @override //可以写也可以不写
  void turnOn() {...}
  // ···
}
```

什么时候extends，什么时候implements？

复用抽象类里面的方法用extends，如果是把抽象类当成标准就使用implements

### （8）枚举类型

```dart
enum Color { red, green, blue }
print(Color.red.index == 0);
print(Color.green.index == 1);
print(Color.blue.index == 2);
```

### （9）为类添加功能： Mixin

实现类似多继承的功能，类是没法实现多继承的，只能实现多个接口

Mixin是复用类代码的途径

使用关键字with

不能有构造函数

```dart
class Musician extends Performer with Musical {
  // ···
}

class Maestro extends Person with Musical, Aggressive, Demented {
	Maestro（String name,num age）:super(name,age);//实现继承类Person
}
//然后Maestro可以使用Person,Musical, Aggressive, Demented里面的所有方法
```

如果不希望作为常规类使用，可以使用mixin代替class

### （10）类变量和方法

static关键字实现类范围的变量和方法

```dart
class Person {
    static String name = "zhangsan";
    int age = 20;
    static void show(){
        print(name);
    }
    void printInfo(){// 非静态可以访问静态也可以访问非静态
        print(name);//访问静态属性
        print(this.age);//访问非静态方法
    }
}
void main() {
    print(Person.name);//直接访问静态成员和方法
    Person.show();
    var person1 = new Person();
    person1.printInfo();//调用非静态方法 必须先实例化
}
// 静态成员 不可以访问非静态的成员和方法，非静态可以访问静态
```

静态变量只到它们被使用的时候才会初始化。

静态方法（类方法）不能在实例上使用，因此它们不能访问 this 

### （11）一个类实现多个接口

```
abstract class A{}
abstract class B{}
class C implements A,B{
}
```



## 9.泛型

基础数组类型 List 的实际类型是 List<E> 。 <…> 符号将 List 标记为 泛型 (或 参数化) 类型

泛型可以指定支持的类型，提高代码质量减少重复的代码

```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42); // 错误 检验类型提高代码质量
//如果只是类型不同的接口，可以使用泛型，减少重复代码
```

### （1）使用集合字面量

给List、Set、Map添加type前缀

```dart
var names = <String>['Seth', 'Kathy', 'Lars'];//List
var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};//Set
var pages = <String, String>{ //Map字面量声明
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```

### （2）指定泛型类型

```dart
var nameSet = Set<String>.from(names); // 指定Set的泛型类型
```

### （3）限制泛型类型

```dart
class Foo<T extends SomeBaseClass> {//使用 extends 实现参数类型的限制
  // Implementation goes here...
  String toString() => "Instance of 'Foo<$T>'";
}

class Extender extends SomeBaseClass {...}
// 使用 SomeBaseClass 或其任意子类作为通用参数
var someBaseClassFoo = Foo<SomeBaseClass>();
var extenderFoo = Foo<Extender>();
```

### （4）使用泛型函数

```dart
T first<T>(List<T> ts) {
  // Do some initial work or error checking, then...
  T tmp = ts[0];
  // Do some additional checking or processing...
  return tmp;
}
```

## 10.库和可见性

import 和 library 指令可以用来创建一个模块化的，可共享的代码库。库可以通过包来分发

### （1）使用库

```dart
import 'dart:html';
import 'package:test/test.dart';
```

### （2）指定库的前缀

如果导入两个存在冲突标识符的库， 则可以为这两个库，或者其中一个指定前缀

```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// 使用 lib1 中的 Element。
Element element1 = Element();

// 使用 lib2 中的 Element。
lib2.Element element2 = lib2.Element();
```

### （3）导入库的一部分内容

```dart
// Import only foo.
import 'package:lib1/lib1.dart' show foo;

// Import all names EXCEPT foo.
import 'package:lib2/lib2.dart' hide foo;
// 延迟加载
import 'package:greetings/hello.dart' deferred as hello;
Future greet() async {
  await hello.loadLibrary();// 直到库加载完成
  hello.printGreeting();
}
```

## 11.异步支持

async...await实现异步操作

### （1）处理Future

```dart
Future checkVersion() async {
    try{
          var version = await lookUpVersion();
  		// Do something with version
    }catch(e){
        //捕获异步操作await函数造成的错误
    }
}
```

```dart
Future<String> lookUpVersion() async => '1.0.0';
```

### （2）处理Stream

当需要从 Stream 中获取数据值时， 可以通过一下两种方式：

- 使用 async 和 一个 异步循环 （await for）。
- 使用 Stream API, 更多详情，参考 in the library tour。

```dart
//异步for循环的使用形式
await for (varOrType identifier in expression) {
  // Executes each time the stream emits a value.
}
```

## 12.生成器

延迟生成( lazily produce )一系列值,使用生成器函数

- Synchronous 生成器： 返回一个 Iterable 对象。
- Asynchronous 生成器： 返回一个 Stream 对象。

```dart
// sync*实现同步生成器函数
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;// 使用 yield 语句来传递值
}
// async*生成异步生成器函数
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;// 使用 yield 语句来传递值
}
// 生成器是递归的，就使用yield*提高性能
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```

## 13.可调用类与Isolates

把类当做函数来使用

```dart
class WannabeFunction {
  call(String a, String b, String c) => '$a $b $c!';
}

main() {
  var wf = new WannabeFunction();
  var out = wf("Hi","there,","gang");
  print('$out');
}
```

 **Isolates**

所有的代码都在隔离区（Isolates）内运行，而不是线程，每个隔离区都有自己的内存堆，确保隔离区的状态不会被其他隔离区访问，互不影响

## 14.Typedefs

typedef会记录保存类型信息

```dart
typedef Compare = int Function(Object a, Object b);//保存
```

判断类型还可以使用is

## 15.元数据

使用元数据可以提供有关代码的其他信息。 元数据注释以字符 @ 开头

对于所有 Dart 代码有两种可用注解：@deprecated 和 @override

## 16.注释

注释可以用//,/**/,///
