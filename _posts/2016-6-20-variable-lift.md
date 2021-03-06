---
layout: post
title:  详解JavaScript变量提升
date:   2016-6-20 00:00:00 +0800
tag: JavaScript
---
* content
{:toc}
变量在程序中随处可见。它们是一些始终在相互影响，相互作用的的数据和逻辑。正是这些互动使应用程序活了起来。
<br/>
<br/>
在JavaScript中使用变量很重要的一方面就是变量的提升 —— 它决定了一个变量何时可以被你的代码使用。如果你在寻找关于这方面的详细介绍，那你算是来对地方了。让我们一起看看吧。
<!-- more -->


### 简介

提升是一种将变量和函数的声明移到函数作用域(如果不在任何函数内的话就是全局作用域)最顶部的机制。

提升影响了变量的生命周期，一个变量的生命周期包含3个阶段：

+ 声明 - 创建一个新变量，例如var myValue
+ 初始化 - 用一个值初始化变量 例如myValue = 150
+ 使用 - 使用变量的值 例如alert(myValue)

这个过程通常是像这样执行的：首先声明一个变量，然后用一个值给它初始化，最后就是使用它。让我们看一个例子：

```js
// 声明
var strNumber;
// 初始化
strNumber = '16';
// 使用
parseInt(strNumber); // => 16
```

在程序中一个函数可以先声明，后使用。初始化被忽略掉了。例如：

```js
// 声明
function sum(a, b) {
  return a + b;
}
// 使用
sum(5, 6); // => 11
```

当这三个步骤按顺序执行的时候，一切看起来都很简单，自然。如果可以话，在使用JavaScript编程的时候你应该遵循这种模式。

JavaScript并没有严格遵循这个顺序，因此提供了更多的灵活性。比如，函数的使用可以在声明之前。下边的例子先调用了函数double(5)，然后才声明该函数function double(num) {...}：

```js
// 使用
double(5); // => 10
// 声明
function double(num) {
  return num * 2;
}
```

这是因为JavaScript中的函数声明会被提升到作用域顶部。

变量提升在不同方面的影响也不同：

+ 变量声明: 使用var, let或const关键字
+ 函数声明: 使用function () {...}语法
+ 类声明: 使用class关键字

接下来让我们详细看看这些区别。


### 函数作用域变量: var

变量声明在函数作用域内创建并初始化一个变量，例如var myVar, myVar2 = 'Init'。默认情况下，声明但是未初始化的变量的值是undefined。
自从JavaScript的第一版本开始，开发者就在使用这种方式声明变量。

```js
// 声明变量num
var num;
console.log(num); // => undefined
// 声明并初始化变量str
var str = 'Hello World!';
console.log(str); // => 'Hello World!'
```

提升与var

使用var声明的变量会被提升到所在函数作用域的顶部。如果在声明之前访问该变量，它的值会是undefined。

假定myVariable在被var声明前被访问到。在这种情况下，声明操作会被移至double()函数的顶部同时该变量会被赋值undefined：

```js
function double(num) {
  console.log(myVariable); // => undefined
  var myVariable;
  return num * 2;
}
double(3); // => 6
```

JavaScript在执行代码时相当于把var myVariable移动到了double()的顶部，就像下面这样：

```js
function double(num) {
  var myVariable;          // 被移动到顶部
  console.log(myVariable); // => undefined
  return num * 2;
}
double(3); // => 6
```

var语法不仅可以声明变量，还可以在声明的同时赋给变量一个初始值：var str = 'initial value'。当变量被提升时，声明会被移动到作用域顶部，但是初始值的赋值却会留在原地：

```js

function sum(a, b) {
  console.log(myString); // => undefined
  var myString = 'Hello World';
  console.log(myString); // => 'Hello World'
  return a + b;
}
sum(16, 10); // => 26
```

var myString被提升到作用域的顶部，然而初始值的赋值操作myString = 'Hello World'不会受到影响。上边的代码等价于下边的形式：

```js
function sum(a, b) {
  var myString;             // 提升到顶部
  console.log(myString);    // => undefined
  myString = 'Hello World'; // 赋值不受影响
  console.log(myString);    // => 'Hello World'
  return a + b;
}
sum(16, 10); // => 26 
```

### 块级作用域变量: let

let声明在块级作用域内创建并初始化一个变量：let myVar, myVar2 = 'Init'。默认情况下，声明但是未初始化的变量的值是undefined。

let是ECMAScript 6引入的一个巨大改进，它允许代码在代码块的级别上保持模块性和封装性：

```js

if (true) {
  // 声明块级变量
  let month;
  console.log(month); // => undefined  
  // 声明并初始化块级变量
  let year = 1994;
  console.log(year); // => 1994
}
// 在代码块外部不能访问month和year变量
console.log(year); // ReferenceError: year is not defined
```

提升与let

使用let定义的变量会被提升到代码块的顶部。但是如果在声明前访问该变量，JavaScript会抛出异常ReferenceError: is not defined。

在声明语句一直到代码库的顶部，变量都好像在一个临时死亡区间中一样，不能被访问。

让我们看看以下的例子：

```js
function isTruthy(value) {
  var myVariable = 'Value 1';
  if (value) {
    /**
     * myVariable的临时死亡区间
     */
    // Throws ReferenceError: myVariable is not defined
    console.log(myVariable);
    let myVariable = 'Value 2';
    // myVariable的临时死亡区间至此结束
    console.log(myVariable); // => 'Value 2'
    return true;
  }
  return false;
}
isTruthy(1); // => true
```

从let myVariable一行一直到代码块开始的if (valaue) {...}都是myVariable变量的临时死亡区间。如果在此区间内访问该变量，JavaScript会抛出ReferenceError异常。

一个有趣的问题出现了：myVariable真的被提升到代码块顶部了吗？还是在临时死亡区间内未定义呢？当一个变量未被定义时，JavaScript也会抛出ReferenceError。

如果你观察一下该函数的开始部分就会发现，var myVariable = 'Value 1'在整个函数作用域内定义了一个名为myVariable的变量。在if (value) {...}块内，如果let定义的变量没有被提升，那么在临时死亡区间内myVariable的值就会是'Value1'了。由此我们可以确认块级变量确实被提升了。

let在块级作用域内的提升保护了变量不受外层作用域影响。在临时死亡区间内访问let定义的变量时抛出异常会促使开发者遵循更好的编码实践：先声明，后使用。

这两个限制是促使在封装性和代码流程方面编写更好的JavaScript的有效途径。这是基于var用法教训的结果 —— 允许在声明之前访问变量很容易造成误解。

### 常量: const


常量声明在块级作用域内创建并初始化一个常量：const MY_CONST = 'Value', MY_CONST2 = 'Value 2'。看看下边的示例：

```js
const COLOR = 'red';
console.log(COLOR); // => 'red'
const ONE = 1, HALF = 0.5;
console.log(ONE);   // => 1
console.log(HALF);  // => 0.5
```

当声明一个变量时，必须在同一条语句中对该变量进行初始化。在声明与初始化之后，变量的值不能被修改。

```js
const PI = 3.14;
console.log(PI); // => 3.14
PI = 2.14; // TypeError: Assignment to constant variable
```

提升与const

使用const定义的常量会被提升到代码块的顶部。

由于存在临时死亡区间，常量在声明之前不能被访问。如果在声明之前访问常量，JavaScript会抛出异常：ReferenceError: is not defined。

const声明常量的提升的效果与使用let声明变量的提升效果相同。

让我们在double()函数内声明一个常量:

```js
function double(number) {
   // 常量TWO的临时死亡区间
   console.log(TWO); // ReferenceError: TWO is not defined
   const TWO = 2;
   // 常量TWO的临时死亡区间至此结束
   return number * TWO;
}
double(5); // => 10
```

由于在声明之前使用常量会导致JavaScript抛出异常。因此使用常量时应该始终先声明，初始化，然后再使用。

### 函数声明

函数声明使用提供的名称和参数创建一个函数。

一个函数声明的例子：

```js
function isOdd(number) {
   return number % 2 === 1;
}
isOdd(5); // => true
```

function isOdd(number) {...}就是一段定义函数的声明。isOdd()用来验证一个数字是否是奇数。

提升与函数声明

函数声明的提升允许你在所属作用域内任何地方使用该函数，即使是在声明之前也可以。换句话说，函数可以在当前作用域或子作用域内的任何地方访问(不会是undefined值，没有临时死亡区间，不会抛出异常)。

这种提升的行为非常灵活。不管是先使用，后声明，还是先声明，后使用都可以如你所愿。

下边的例子在开始的地方调用了一个函数，然后才定义它：


```js

// 调用被提升的函数
equal(1, '1'); // => false
// 函数声明
function equal(value1, value2) {
   return value1 === value2;
}
```

这段代码可以正常执行是因为equal()被提升到了作用域顶部。

需要注意的函数声明function () {...}和函数表达式 var = function() {...}之间的区别。两者都用于创建函数，但是却有着不同的提升机制。

下边的例子演示了该区别：

```js
// 调用被提升的函数
addition(4, 7); // => 11
// 变量被提升了，但值是undefined
substraction(10, 7); // TypeError: substraction is not a function
// 函数声明
function addition(num1, num2) {
   return num1 + num2;
}
// 函数表达式
var substraction = function (num1, num2) {
  return num1 - num2;
};
```


addition被彻底的提升并且可以在声明之前被调用。

然而substraction是使用变量声明语句声明的，虽然也被提升了，但被调用时值是undefined。因此会抛出异常TypeError: substraction is not a function。

### 类声明

类声明使用提供的名称和参数创建一个构造函数。类是ECMAScript 6中引入的一项巨大改进。

类建立在JavaScript的原型继承之上并提供了诸如super(访问父类)，static(定义静态方法)，extends(定义子类)之类的额外功能。

一起看看如何声明一个类并创建一个实例：

```js
class Point {
   constructor(x, y) {
     this.x = x;
     this.y = y;
   }
   move(dX, dY) {
     this.x += dX;
     this.y += dY;
   }
}
// 创建实例
var origin = new Point(0, 0);
// 调用实例的方法
origin.move(50, 100);
```

提升与class

类声明会被提升到块级作用域的顶部。但是如果你在声明前使用类，JavaScript会抛出异常ReferenceError: is not defined。所以正确的方法是先声明类，然后再使用它创建实例。

类声明的提升与let定义变量的提升效果类似。

让我们看看在声明之前创建类实例会怎样：

```js
// 使用Company类
// 抛出ReferenceError: Company is not defined
var apple = new Company('Apple');
// 类声明
class Company {
  constructor(name) {
    this.name = name;
  }
}
// 在声明之后使用Company类
var microsoft = new Company('Microsoft');
```

和预期的一样，在类定义之前执行new Company('Apple')会抛出ReferenceError异常。这很不错，因为JavaScript鼓励先声明后使用的方式。

还可以用使用了变量声明语句(var，let和const)的类表达式创建类。让我们看看下面的场景：

```js
// 使用Sqaure类
console.log(typeof Square);   // => 'undefined'
//Throws TypeError: Square is not a constructor
var mySquare = new Square(10);
// 使用变量声明语句声明类
var Square = class {
  constructor(sideLength) {
    this.sideLength = sideLength;
  }
  getArea() {
    return Math.pow(this.sideLength, 2);
  }
};
// 在声明之后使用Square类
var otherSquare = new Square(5);
```

这个类使用变量声明语句var Square = class {...}定义。Square变量被提升到了作用域的顶部，但是直到声明该变量的这行代码之前其值都是undefined。因此var mySquare = new Square(10)语句相当于把undefined当作构造函数使用，这导致JavaScript抛出TypeError: Square is not a constructor异常。

### 最后的想法

像在本文阐述的那样，JavaScript中的提升有多种形式。即使你知道它的工作方式，一般还是建议按照声明 > 初始化 > 使用的顺序使用变量。通过let，const和class提升的方式可以看出ECMAScript 6强烈支持这种使用方式。这将使你免于遇见不期望的变量，undefined以及ReferenceError的困扰。