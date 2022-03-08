### 1.ES5/ES6 的继承除了写法以外还有什么区别

![image-20200429183127342](images/image-20200429183127342.png)

直接将父类的一个实例赋给子类的原型等方式，组合继承那些，apply，call等

```javascript
function Person(name){
 this.name=name;
 this.className="person" 
}
Person.prototype.getClassName=function(){
 console.log(this.className)
}
function Man(){
}
Man.prototype=new Person();
```

![image-20200429183138877](images/image-20200429183138877.png)

es6中：引入了class、extend、super、static

```javascript
class Person{
  //static sCount=0 //1 class内部不能放静态的属性
  constructor(name){
     this.name=name; 
     this.sCount++;
  }
  //实例方法 //2
  getName(){
   console.log(this.name)
  }
  static sTest(){
    console.log("static method test")
  }
}

class Man extends Person{
  constructor(name){
    super(name)//super去调用父类构造函数
    this.sex="male"//this关键字必须在super后面
  }
}
var man=new Man("Davin")
man.getName()
//man.sTest()
Man.sTest()//4 静态函数可以被继承，只能通过类名调用
输出结果：
Davin
static method test
```

es6的继承

```javascript
class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

主要是除了写法之外的区别还有调用的区别

其他区别：

ES6的继承实现在于使用super关键字调用父类，反观ES5是通过call或者apply回调方法调用父类。

### 2.npm安装机制，为什么npm install就可以安装对应的模块

（1）发出`npm install`命令

（2）查询node_modules目录之中是否已经存在指定模块（确定工程中的首层依赖，也就是 dependencies 和 devDependencies 属性中直接指定的模块（假设此时没有添加 npm install 参数）

- 若存在，不再重新安装
- 若不存在
  - npm 向 registry 查询模块压缩包的网址
  - 下载压缩包，存放在根目录下的`.npm`目录里
  - 解压压缩包到当前项目的`node_modules`目录

### 3.发布订阅者模式和观察者模式

![image-20200501171255359](images/image-20200501171255359.png)

观察者模式没中间商赚差价（菜农和买菜的），发布订阅模式（报社邮局和个人） 有中间商赚差价

### 4.实现 (5).add(3).minus(2) 功能。

例： 5 + 3 - 2，结果为 6

```js
Number.prototype.add = function(n) {
  return this.valueOf() + n;
};
Number.prototype.minus = function(n) {
  return this.valueOf() - n;
};
```

### 5.RESTful API

就是用`URI`表示资源，用HTTP方法(GET, POST, PUT, DELETE)表征对这些资源的操作

RESTful API 就是REST风格的API，是基于HTTP的协议，有设计规范和原则

比如

1. 将API部署在专用域名之下，API很简单，可以放主域名下

```javascript
https://example.org/api/
```

2. 统一接口。对于业务数据的CRUD，RESTful 用HTTP方法与之对应

- GET（SELECT）：从服务器取出资源（一项或多项）。
- POST（CREATE）：在服务器新建一个资源。
- PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
- DELETE（DELETE）：从服务器删除资源。

- HEAD：获取资源的元数据。
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的

比如：

- GET /zoos：列出所有动物园
- POST /zoos：新建一个动物园
- GET /zoos/ID：获取某个指定动物园的信息
- PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
- PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
- DELETE /zoos/ID：删除某个动物园
- GET /zoos/ID/animals：列出某个指定动物园的所有动物
- DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物

3.错误信息返回，返回的信息中将error作为键名，出错信息作为键值即可。

**其实就是各种统一的机制，方便不同的前端设备与后端进行通信，为了设计一套合理的、好用的API**