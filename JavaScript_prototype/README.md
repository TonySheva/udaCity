
构造函数：Constructor

构造函数就是一个普通的函数，内部使用this变量。
这个this变量会绑定在 new 构造函数生成出来的实例对象上

function Human(name, color) {
   this.name = name;
   this.color = color;
}

var human1 = new Human('tony', 'yellow');

var human2 = new Human('sheva', 'white');

console.log(human1.name); //tony
console.log(human2.color); //white

实例对象human1和human2 自动含有一个constructor 属性，
指向它们的构造函数

console.log(human1.constructor == Human) // true
console.log(human2.constructor == Human) // true

instanceof 用于验证原型对象与实例对象之间的关系

console.log(human1 instanceof Human); //true
console.log(human2 instanceof Human); //true

当我们给Human对象继续增加属性和方法：
function Human(name, color) {
   this.name = name;
   this.color = color;
   this.type = ‘哺乳类动物’;
   this.run = function(){ console.log("run!");};
}

这次我们再次new两个实例
var human1 = new Human('tony', 'yellow');

var human2 = new Human('sheva', 'white');

console.log(human1.type) //哺乳类动物

console.log(human2.run) // run!

当我们需要的一些属性和方法是不变的时候，这样生成
不同的实例，会占用更多的内存

console.log(human1.type == human2.type);  //false

让这些不变的属性和方法在内存只生成一次，
且所有实例都指向一个内存地址。

使用Prototype：

每一个构造函数/原型对象都有一个prototype属性，指向另一个对象
这个对象的所有属性和方法都会被构造函数的实例继承
_proto_有些浏览器会显示，有些没有。
不允许直接修改_proto_

上面的例子可以改写为

function Human(name, color) {
   this.name = name;
   this.color = color;
}

Human.prototype.type = '哺乳类动物';

Human.prototype.run = function(){ console.log("run!");};

再次生成实例
var human1 = new Human('tony', 'yellow');

var human2 = new Human('sheva', 'white');

console.log(human1.type) //哺乳类动物

console.log(human2.run) // run!

此时实例的type属性和run()方法都是同一个内存地址中。

console.log(human1.run == human2.run); //true

Prototype的一些方法：

isPrototypeOf():

用来判断某个prototype对象和某个实例直接的关系

console.log(Human.prototype.isPrototypeOf(human1); //true
console.log(Human.prototype.isPrototypeOf(human2); //true

hasOwnProperty():

每一个实例对象都会有一个hasOwnProperty()方法，用来判断某一个
属性到底是本地属性还是继承自prototype对象的属性

console.log(human1.hasOwnProperty("name")); //true

console.log(human1.hasOwnProperty("type")); //false

in 运算符可以判断，某个实例是否含有某个属性（包括本地和继承）

console.log("name" in human1); //true
console.log("type" in human1); //true

所以in可以用来遍历某个对象所有的属性

for(var prop in human1 ) {console.log("human1["+prop+"]="+human1[prop]);}

构造函数继承：

1.直接在子类（构造函数）中绑定父类（构造函数）

function Animal() {
　　this.species = "动物";
} //parent

function Human(name, color) {
   Animal.apply(this, arguments);
   this.name = name;
   this.color = color;
}

var human1 = new Human("tony", "yellow");

console.log(human1.species); //动物


2.prototype 继承

当human的prototype对象，指向Animal的实例。

那么human的实例对象，都继承Animal

Human.prototype = new Animal();   //删除了prototype对象原先的值，赋予了新的值

Human.prototype.constructor = Human; // ×任何一个prototype对象都有一个constructor属性，指向它的构造函数，所以要将它从Animal 指回Human

var human1 = new Human("tony", "yellow");

console.log(human1.species);  //动物

每一个实例都有一个constructor属性，默认调用prototype对象的constructor属性

console.log(human1.constructor == Human.prototype.constructor)

如果替换了prototype对象，下一步要为新的prototype对象的construtor指回原来的构造函数

o.prototype = {};

o.prototype.constructor = o;

ES5.1 之后加了几个新的 API 帮助我们操作对象的 [[Prototype]]，自此以后 JavaScript 才真的有自由操控原型的能力。它们是：

Object.prototype.isPrototypeOf
Object.create
Object.getPrototypeOf
Object.setPrototypeOf

var parent = {
  _name: 'Tony',
  getName: function() { return this._name },
}

var child = Object.create(parent)

Object.getPrototypeOf(child)           // parent
parent.isPrototypeOf(child)            // true
Object.prototype.isPrototypeOf(child)  // true
child instanceof Object                // true

var anotherParent = {
  name: 'Alex'
}

Object.setPrototypeOf(child, anotherParent)
Object.getPrototypeOf(child)  // anotherParent

// 修改数组的 [[Prototype]]
var a = []
Object.setPrototypeOf(a, anotherParent)
a instanceof Array        // false
Object.getPrototypeOf(a)  // anotherParent



function Parent() {}
function Child() {}

Child.prototype = Object.create(Parent.prototype)
Child.prototype.constructor = Child




3.直接继承prototype

继承不变的属性可以用它：

function Animal() {}
Animal.prototype.species = '动物';

Human.prototype = Animal.prototype;

Human.prototype.constructor = Human;

var human1 = new Human("tony", "yellow");

console.log(human1.species); //动物 

此时
console.log(Animal.prototype.constructor); // Human 

被改变了。

4.利用空对象作为中介

var F = function() {};

F.prototype = Animal.prototype;

Human.prototype = new F();

Human.prototype.constructor = Human;

F为空对象，几乎不占内存，此时修改Human的对象，Animal不会受影响

console.log(Animal.prototype.constructor); // Animal

function extend(Child, Parent) {

　　var F = function(){};
　　F.prototype = Parent.prototype;
　　Child.prototype = new F();
　　Child.prototype.constructor = Child;
　　Child.uber = Parent.prototype; // 指向父对象的prototype属性，可以直接调用父对象的方法。实现继承完备性。
}

使用时：

extend(Human, Animal);

var human1 = new Human("tony", "yellow");

console.log(human1.species); //动物

5.copy 继承

function Animal () {}

Animal.prototype.species = "动物";

function extend2(Child, Parent) {
　　var p = Parent.prototype;
　　var c = Child.prototype;
　　for (var i in p) {
　　　　c[i] = p[i];
　　　　}
　　c.uber = p;
}

将父对象的prototype对象中的属性，（值类型）一一拷贝给child的prototype对象

extend2(Human, Animal);

var huam1 = new Huam("tony", "yellow");

console.log(huam1.species); // 动物

如果这时候parent的某个属性是一个数组或者另一个对象，这个时候浅拷贝就不行了

因为现在变成了引用类型，需要深拷贝

function deepCopy(p, c) {
　　　　var c = c || {};
　　　　for (var i in p) {
　　　　　　if (typeof p[i] === 'object') {
　　　　　　　　c[i] = (p[i].constructor === Array) ? [] : {};
　　　　　　　　deepCopy(p[i], c[i]);
　　　　　　} else {
　　　　　　　　　c[i] = p[i];
　　　　　　}
　　　　}
　　　　return c;
　　}

obejct() 方法

function object(o) {
　　function F() {}
　　F.prototype = o;
　　return new F();
}

将子对象的prototype属性 指向父对象。


ES6：

class User {
  constructor(firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }

  fullName() {
    return `${this.firstName} ${this.lastName}`
  }
}

let user = new User('Tony', 'Sheva')
user.fullName()  // Tony Sheva


跟以下是一个意思：

function User(firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}

User.prototype.fullName = function() {
  return '' + this.firstName + this.lastName
}

es6：
class Child extends Parent {   
  constructor(firstName, lastName, age) {
    super(firstName, lastName)
    this.age = age
  }
}

等价于：

function Child(firstName, lastName, age) {
  Parent.call(this, firstName, lastName)
  this.age = age
}

Child.prototype = Object.create(Parent.prototype)
Child.constructor = Child


super在子类的contrcutor里必须先调用
它在编译时就确定了。而不是运行的时候。

class Child extends Parent {
  static findAll() {
    return super.findAll()
  }
}

等价于：
findAll() {
  return Parent.findAll()
}


