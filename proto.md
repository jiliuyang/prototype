# 原型和原型链解读

原型和原型链的概念来源于js的继承机制
## 原型

### 一、为什么要使用原型？

假如，我们现在有一个构造函数Dog:
```
function Dog(name) {
  this.name = name;
  this.voice = function () {
    alert("旺旺！")
  }
}
var Dog1 = new Dog("花花");
var Dog2 = new Dog("琪琪");
```
我们又根据该构造函数创建出2个实例对象，这两个实例对象都包含了一个name属性和voice方法。这本来没有什么，但考虑到函数本身就是一个对象，会在内存中单独占用一块空间，这就导致了2个实例对象中的2个voice方法都会占用部分内存空间，而这2个方法的返回结果是一样的，继而就造成了内存空间的浪费。如果这样还不明显，那假设我们由此构造函数创建了上百个实例对象，每个实例对象中的voice方法函数都会占用部分内存，累加起来的话内存空间就造成了极大的浪费。

那么要怎么解决这种空间浪费呢？那就是提供一个共享的空间，用来存放一些公共的属性和方法，而这个共享的空间就是原型。

### 二、什么是原型？

原型可以从两个方面来说：
1、针对构造函数，它拥有一个prototype属性，且有且只有函数才拥有该属性。prototype属性指向一个对象，这个对象存放着一些共用的属性和方法，也就是我们所说的原型对象，又叫显式原型。

2、针对实例对象，它拥有一个__proto__属性，这个属性是所有对象都拥有的，又叫隐式原型。它指向的是其构造函数的prototype属性，拿上面的例子来说就是：
```
Dog1.__proto__ === Dog2.__proto__ === Dog.prototype
```
备注：__proto__是每个对象都有的属性，但其并不是一个规范属性，只是部分浏览器对应的属性，标准属性仍旧是prototype。

在js语言中，万事万物皆为对象，而js通常被描述成一种基于原型的语言，每个对象都拥有一个原型对象，对象以其原型为模板，从原型中继承共有的方法和属性。

实际上不同的对象创建方式，它的原型是有区别的。

```
    //三种对象构造方式

    //字面量方式      此时a1.__proto__ === Object.prototype
    var a1 = {};
    
    //构造器方式      此时a2.__proto__ === A.prototype
    var A = function () {};
    var a2 = new A();

    //Obiect.create()方式     此时b3.__proto__ = a3
    var a3 = { b: 1 };
    var b3 = Object.create(a3);
```
具体可以自己私下测试验证下。

## 原型链

JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾,这就形成了一个原型链(原型链的最末尾是null)。而假使最终也没有找到，才会判定其为undefined.

就拿篇首的例子来说，我们给Dog函数的原型添加新的共有属性并给Dog1添加新的私有属性：

```
Dog.prototype.classify = "犬类";
Dog1.hobby = "睡觉";
console.log(Dog1)
```
此时打印出来的结果是：

![手翻书](/img/img1.png) 

我们可以看到它基于__proto__呈现出一条原型链，通过该链来实现属性和方法的继承。

### 原型链查找的性能问题

在原型链上查找属性比较耗时，对性能有副作用，这在性能要求苛刻的情况下很重要。另外，试图访问不存在的属性时会遍历整个原型链。所以最好不要随便查找本来就没有的属性。

遍历对象的属性时，原型链上的每个可枚举属性都会被枚举出来。要检查对象是否具有自己定义的属性，而不是其原型链上的某个属性，则必须使用所有对象从 Object.prototype 继承的 hasOwnProperty 方法。