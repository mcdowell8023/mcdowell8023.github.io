---
title: JavaScript从头来2.2-对象
comments: true
mathjax: true
categories:
  - 基础知识
tags:
  - js基础
  - 追本溯源
  - 笔记
date: 2020-08-27 19:52:35
---

> javascript 中 万物皆对象。
> 对象只是带有属性和方法的特殊数据类型
> 对象只是一种特殊的数据。对象拥有属性和方法
> js 中的引用类型都派生自 对象（Object），如 Array Function Math 等等
> 包括 常见的 基本类型(值类型) string 、 number 的值 均为对应的 String 、 Number 调用 valueOf 或者 toString 得到的
> 可见，对象在 javascript 中的地位。 在 javascript 的世界里，对象就是属性的集合

# 对象

从形式上来定义 JavaScript 的对象，是一种属性的集合。【 属性值可以是 函数（function）】

# 创建对象

1. 使用 **对象字面量** 创建对象

```js
var person = {
  name: 'mcdowell',
  age: 29,
}
person.sayName = function () {
  alert(this.name)
}
```

> 使用{}创建对象，等同于 new Object();

2. 使用 **new 借助 Object 函数** 创建对象

```js
var person = new Object()
person.name = 'mcdowell'
person.age = 29
person.sayName = function () {
  alert(this.name)
}
```

> 问题：1.代码冗余； 2.对象中的方法不能共享，每个对象中的方法都是独立的

3. 使用 **工厂模式** 创建对象

```js
function createPerson(name, age) {
  var person = new Object()
  person.name = name
  person.age = age
  person.sayName = function () {
    alert(this.name)
  }
  return person
}

var p1 = createPerson('mcdowell', 29)
var p2 = createPerson('bart', 31)
console.log(p1, 'p1,p2', p2)

console.log(p1.constructor === p2.constructor) //true
console.log(p1.constructor === createPerson) //false
console.log(p1 instanceof Object) //true
console.log(p2 instanceof Object) //true
console.log(p1.sayName === p2.sayName) //false
```

> 工厂函数每执行一次，都会创建一个对象。
> 每个创建的 对象都拥有**各自独立的方法和属性**。
> 对象的 constructor 构造函数共同指向 Object 方法

> 优点：避免部分代码重复，减少冗余
> 缺点：每个对象 调用的还是不同的方法 ( 方法没有实现复用 )

4. 通过**构造函数**创建对象

```js
function Person(name, age) {
  this.name = name
  this.age = age
  this.sayName = function () {
    alert(this.name)
  }
}
var p1 = new Person('mcdowell', 29)
var p2 = new Person('bart', 31)
//
console.log(p1, 'p1,p2', p2)

console.log(p1.constructor === p2.constructor) //true
console.log(p1.constructor === Person) //true
console.log(p1 instanceof Object) //true
console.log(p2 instanceof Object) //true
console.log(p1.sayName === p2.sayName) //false
```

> 每 new 一次 构造函数，都会创建一个对象
> 每个创建的 对象都拥有**各自独立的方法和属性**。
> 对象的 constructor 构造函数共同指向 Person 方法

> 优点：进一步优化，避免部分代码重复，减少冗余
> 缺点：每个对象 调用的还是不同的方法 ( 方法没有实现复用 )

5. 通过 **原型模式** 创建对象

```js
function Person() {}

Person.prototype.name = 'person'
Person.prototype.sayName = function () {
  console.log(this.name, 'name')
}

var p1 = new Person()
var p2 = new Person()

p1.sayName()

console.log(p1.sayName === p2.sayName) // true
console.log(Person.prototype.constructor) // function Person(){}
console.log(Person.prototype.constructor == Person) // true

console.log(p1.__proto__ === p2.__proto__) // true
```

> 每 new 一次 构造函数，都会创建一个对象
> 每个创建的 对象都 都通过 **proto** 指向 Person.prototype 对象，从而实现 属性方法共享。
> 对象的 constructor 构造函数共同指向 Person 方法

> 优点： 真正意义实现了复用
> 缺点： 各对象 也需要自己特有属性 方法

6. 通过 **原型模式 + 构造函数** 创建对象

```js
function createPerson(name, age) {
  this.name = name
  this.age = age
}
createPerson.protoType.sayName = function () {
  alert(this.name)
}；

var p1 = new createPerson('mcdowell', 29)
var p2 = new createPerson('bart', 31)
//
console.log(p1, 'p1,p2', p2)

console.log(p1.constructor === p2.constructor) //true
console.log(p1.constructor === createPerson) //true
console.log(p1 instanceof Object) //true
console.log(p2 instanceof Object) //true
console.log(p1.sayName === p2.sayName) //false

```

> 优点：每个对象 都会有自己的一份 属性的副本，但又同时共享着对方法的引用，最大限度地节省了内存。
> 继承：需要复用的放在 protoType 属性指向的对象下 ，通过 原型链 的方式 实现复用
> 多态：不同对象 的特殊属性（多态） 借助构造函数完成

7. 通过 **Object.create** 创建对象

> 语法：Object.create(proto, [propertiesObject])
> proto:必须。表示**新建对象的原型对象**，即该参数会被赋值到目标对象(即新对象，或说是最后返回的对象)的原型上。该参数可以是 null、对象、函数的 prototype 属性
> 可选。 添加到新创建对象的可枚举属性（即其自身的属性，而不是原型链上的枚举属性）对象的属性描述符以及相应的属性名称。这些属性对应 **Object.defineProperties** 的第二个参数。

```js
var object = Object.create(null) // {} 创建了一个空对象
// 创建一个人
// 自带 头发，皮肤 属性
// 人类 类型无法改变
// 用第二个参数创建非空对象的属性描述符默认是为false的，
// 不可写,不可枚举,不可配置
var p1 = Object.create(
  { hair: 'black', skin: 'yellow' },
  {
    type: {
      value: 'person',
      writable: false,
      enumerable: false,
      configurable: false,
    },
  }
)
// 给上名字 和 年龄
p1.name = 'mcdowell'
p1.age = '20'
console.log(p1)
```

> 与 new Object 创建方式不同；new Object 创建的对象 原型 指向的原型对象一直都存在
> Object.create(null) 创建的对象 为真正意义上的空对象，原型 为 undefined
> Object.create 直接以 传入对象 作为 原型 进行创建的
> Object.create 创建的对象 的 constructor 与 new Object 创建的对象 的 constructor **都指向 Object 构造函数**

8. 通过 **class** 创建对象

> 类 不会像函数一样 去进行变量提升

```js
// 父类
class Person {
  // 公有属性
  type = 'human'
  // 私有属性，外部无法访问（会报错）
  // 读取私有属性的方法 this.#study
  #study = 'true'
  constructor(name, age) {
    // constructor 上来就执行，相当于 构造函数 本身属性
    this.name = name
    this.age = age
  }

  // 定义静态 属性 同 静态方法，无法在实例中 读取，仅在 构造函数 属性说明
  static description = 'person'
  // static 关键字用来定义一个类的一个静态方法。
  // 调用静态方法不需要实例化该类，但不能通过一个类实例调用静态方法。
  // 静态方法通常用于为一个应用程序创建工具函数。
  static create(name, age) {
    var person = new Person(name, age)
    console.log('创建了一个人', person)
    return person
  }
  // 相当于 构造函数 的 原型对象 中 进行声明 函数方法
  sayName() {
    console.log('说出自己的名字：', this.name)
  }
  speak() {
    console.log('介绍自己：', this, '私有属性 #study', this.#study)
  }
}
// 直接访问 外侧静态属性
console.log('输出静态属性description：', Person.description)
var p1 = Person.create('mcdowell', 20)
p1.sayName()
p1.speak()
```

###### extends 继承

- 继承 class

```js
// 子类
class Man extends Person {
  constructor(...props) {
    // 调用超类构造函数并传入参数
    // 如果子类中定义了构造函数，那么它必须先调用 super() 才能使用 this
    super(...props)
    this.sex = 'man'
  }
  sayName() {
    console.log('我叫', this.name, '我是个男的')
  }
}

var man = new Man('bart', 22)
man.sayName()
man.speak()
```

- 继承 传统 函数类

```js
function Animal() {
  this.name = name
}
Animal.prototype.speak = function () {
  console.log(this.name + ' 发出声音')
}

class Dog extends Animal {
  speak() {
    // 使用 super 关键字 调用 对象的父对象上的函数
    super.speak()
    console.log(this.name + ' 汪汪叫')
  }
}

var dog = new Dog('阿福')
dog.speak()
```

- 类不能继承常规对象（不可构造的）

```js
var Animal = {
  speak: function () {
    console.log(this.name + ' 发出声音')
  },
}

class Dog {
  constructor(name) {
    this.name = name
  }
}
// 通过 Object.setPrototypeOf 继承常规对象
Object.setPrototypeOf(Dog.prototype, Animal)

var dog = new Dog('阿福')
dog.speak()
```

首先，我肯定是需要你告诉我，什么是面向对象，面向对象有哪些特点，以及这些特点的解释。
JavaScript 如何实现这些特点，比如封装、继承、多态。如果关于上述三点，你能够解释到有多少种实现方式、优缺点是什么。以及近几年流行的解决方案是什么。这就是加分 ，比如对于继承吧。类式继承、构造函数继承、组合继承、原型继承、寄生组合继承等等，说出大概的实现思路和优缺点，再介绍下 extends 或者 mixin 的实现甚至你可以衍生到 JavaScript 的模块化发展甚至到为什么现在 TS 如此流行。那么可以说到这一环节解答的就非常棒了。
回答完 JavaScript 的面向对象，是不是可以从此衍生下为什么需要面向对象。以及当先对于软件设计的高内聚、低耦合的思考？来个对此题一个提纲挈领的总结？
