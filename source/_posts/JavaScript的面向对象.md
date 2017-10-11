---
title: JavaScript的面向对象理解
date: 2017-10-11 15:12:35
tags: JavaScript
categories: JavaScript
---

# 0x00 前言

学过其他语言再学javascript时会有些不习惯。面向对象（Object-Oriented， OO）的语言有一个标志，那就是它们都有类的概念，而通过类可以创建任意多个具有相同属性和方法的对象。但是 ECMAScript 中没有类的概念，因

此它的对象也与基于类的语言中的对象有所不同。

既然没有类又如何按面向对象规则编程呢？

还记得面向对象的三大特征：封装、继承、多态。

只要实现这三个特性就是面向对象，并不一定要和类联系到一起，或许这种类式面向对象只是面向对象的一种而已。

那javascript如何实现面向对象？



# 0x01 对象

**ECMA-262 把对象定义为：“无序属性的集合，其属性可以包含基本值、对象或者函数。”**

我们可以把 ECMAScript 的对象想象成散列表：无非就是一组名值对，其中值可以是数据或函数。

javascript创建一个对象也很简单：

```javascript
    var cat = {
        name: "kity",
        age: 3,
        run: function () {
            document.writeln(this.name + "跑起来<br>");
        }
    }
    cat.run();
```

**javascript总对象可以简单理解为键值对集合**

上面定义一个猫对象很简单，2个属性一个方法。但是如果要创建的对象很多的话，每个对象都要把这些属性写一遍很费时费力，更不利于代码维护。这时可以使用构造函数解决同类属性问题。

# 0x02 构造函数

从代码的角度看，构造函数很像返回对象的函数：定义后，每当需要创建新对象时都可调用它。

```javascript
    function Cat(name, age) {
        this.name = name;
        this.age = age;
        this.run = function () {
            document.writeln(this.name + "跑起来<br>");
        }
    }
  var kity = new Cat("kity",3);
```

**注意到我们给构造函数命名时， 采用了首字母大写的方式。 并非必须这样做，但人人都将此视为一种约定加以遵守。与大多数函数中不同， 我们没有使用局部变量， 而是使用关键字this。**

上面只是方便了同类属性每次的声明，但是不同类又如何优化？比如狗类也有这这几个属性。这个时候继承也就应运而生了。

学过其他语言人可能又有疑问了，javascript中又没有类的概念，如何实现继承呢？

# 0X02 继承

继承是 OO 语言中的一个最为人津津乐道的概念。许多 OO 语言都支持两种继承方式：接口继承和
实现继承。 **ECMAScript 只支持实现继承，而且其实现继承主要是依靠原型链来实现的。**

```javascript
    function Animal(name, age) {
        this.name = name;
        this.age = age;
        this.run = function () {
            document.writeln(this.name + "跑起来<br>")
        };
    }
    
    function Dog() {
    }
    function Cat() {
    }
    Dog.prototype = new Animal("贝贝", 11);
    Cat.prototype = new Animal("kity", 3);
    var dog = new Dog();
    var cat = new Cat();
    document.writeln("狗名：" + dog.name + "<br>");
    document.writeln("狗年龄：" + dog.age + "<br>");
    dog.run();

    document.writeln("猫名：" + cat.name + "<br>");
    document.writeln("猫年龄：" + cat.age + "<br>");
    cat.run();
```



# 0x03 封装

上面获取属性值时直接使用`对象.属性名`的方式，这种方式虽然简单但是同样也存在隐患。

如果只想让其他人获取值而不想让人修改值，那这种方式是不能实现的。

虽然javascript中没有像Java那样提供访问权限的public、private关键字，但是可以通过其他方式体现封装的特性：局部变量+内嵌方法。

```javascript
    function Animal(name, age) {
        var name = name;
        var age = age;
        this.run = function () {
            document.writeln(name + "跑起来<br>")
        };
        this.getName = function () {
            return name;
        }
        this.getAge = function () {
            return age;
        }
    }

    function Dog() {
    }
    function Cat() {
    }
    Dog.prototype = new Animal("贝贝", 11);
    Cat.prototype = new Animal("kity", 3);
    var dog = new Dog();
    var cat = new Cat();
    document.writeln("狗名：" + dog.getName() + "<br>");
    document.writeln("狗年龄：" + dog.getAge() + "<br>");
    dog.run();

    document.writeln("猫名：" + cat.getName() + "<br>");
    document.writeln("猫年龄：" + cat.getAge() + "<br>");
    cat.run();
```



# 0x04 多态

多态这个吓人的词汇，是面向对象的特性，意思是一个实例不仅是它自身类的实例，也可以被当做它的任何父类的实例来使用。

在很多面向对象语言中，多态是OOP 中的一个很特殊的特性。而在 JavaScript 中，因为没有类型的概念，所以任何对象可以在任何地方被使用（虽然不能保证正确的结果），从这个角度来说，JavaScript 具备终极的多态性。

接着上面的代码写：

```javascript
    if (dog instanceof Animal){
        document.writeln("True");
    }
```

从打印结果就会发现`dog instanceof Animal`为True。可见dog除了是Dog实例，也是Animal实例。

