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