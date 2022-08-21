# 关于TypeScript你需要了解的知识

TypeScript is a language for application-scale JavaScript.
TypeScript adds optional types, classes, and modules to JavaScript. 
TypeScript supports tools for large-scale JavaScript applications for any browser, for any host, on any OS. 
TypeScript compiles to readable, standards-based JavaScript. 

[TypeScript](https://github.com/Microsoft/TypeScript)

## 搭建本地TypeScript开发环境

- 通过npm安装typescript

```bash
$ sudo npm install -g typescript
```

- 手动编译ts文件

创建一个`hello.ts`的文件，内容为：

```js  
class Hello {  // 创建一个Hello的类
    constructor() {
        console.log('Hello, TypeScript');  // 在构造方法中进行输出
    }
}

let hello = new Hello();  // 创建Hello类的实例
```
通过`typescript`提供的`tsc`指令进行编译
```bash
$ tsc hello.ts 
$ tree ./
./
├── hello.js  # tsc会吧ts文件编译成浏览器可执行的js文件
└── hello.ts

0 directories, 2 files
```
其内容为
```bash
$ cat hello.js 
var Hello = (function () {
    function Hello() {
        console.log('Hello, TypeScript'); // 在构造方法中进行输出
    }
    return Hello;
}());
var hello = new Hello(); // 创建Hello类的实例
```
用浏览器执行一下，看看会发生什么。

然而，聪明的开发者已经会使用`vscode`和`webstorm`进行代码的自动编译了.

## 字符串

- 多行字符串

```ts
console.log(`hello
world`);
```

- 字符串模板

双引号之间的内容会被当做一个字符串进行输出，而``之间的却可以被解析出来

```ts
let lastName: string = 'An';
let firstName: string = "sheng";

function Name(LastName, FirstName) {
    return `${LastName} ${FirstName}`
}

console.log(`My LastName: ${lastName}`);
console.log(`My FirstName: ${firstName}`);
console.log(`My Name: ${Name(lastName, firstName)}`);
```

## 参数

- 参数类型

在参数名称后面使用冒号来指定参数的类型

变量类型

```ts
let myname: string = 'an sheng';
let alias = "xx";

// myname = 13;  // myname常量的类型指定了string，所以赋值的时候必须是string类型
// alias = 13;  // 在创建alias常量的时候，由于值是string类型，所以赋值的时候也必须是string类型
```

基本的类型

|关键字|类型|
|:--|:--|
|string|字符串类型|
|number|整形|
|any|任意类型|
|boolean|布尔类型|

指定函数返回值类型

```ts
function test(): void {  // 返回值可以是任意类型

}

function test(): string {  // 返回值必须是字符串
    return "";
}
```

指定参数类型

```ts
function test(age: number, name: string): string {
    return "";
}
```

自定义类型

```ts
class Person {
    name: string;
    age: number;
}

let zhangsan: Person = new Person();
zhangsan.age = 18;  // 变量值必须是Person类中执行的
zhangsan.name = "Hello";
```

- 参数默认值

在参数声明后面用等号来指定参数的默认值

```ts
let myname: string = 'Hello';  // 变量的默认值

function test(a: string, b: string, c: string = 'ccc') {  // 函数参数的默认值，默认值的参数一定要声明在最后面
    console.log(a);
    console.log(b);
    console.log(c);
}

test("xx", "yy");
```

- 可选参数

在方法的参数声明后面用问好来标明此参数为可选参数

```ts
function test(a: string, b?: string, c: string = 'ccc') {  // 可选参数声明在默认参数的前面
    console.log(a);
    console.log(b);
    console.log(c);
}

test("aaa");
```

## 函数

- Rest and Spread操作符

用来声明任意数量的方法参数

```ts
function func1(...args) {  // 任意数量的参数
    args.forEach(function (arg) {
        console.log(arg)  // 1 2 3 4 5
    })
}
func1(1, 2, 3, 4, 5);
```

```ts
function func1(a, b, c) {
    console.log(a);
    console.log(b);
    console.log(c);
}
let args1 = [1, 2];
let args2 = [5, 6, 7, 8, 9];
func1(...args1);  // 1 2 undefined
func1(...args2);  // 5 6 7
```

- generator函数

控制函数的执行过程，手工暂停和恢复代码执行

```ts
function* doSomething() {
    console.log('start');
    yield;  // 与Python中的yield关键词一样，遇到这个词就暂停
    console.log("finish");
}

let func1 = doSomething();
func1.next();  // start
func1.next();  // finish
```

```ts
function* getStockPrice(stock) {
    while (true) {
        yield Math.random() * 100;
    }
}

let priceGenerator = getStockPrice("IBM");  // 股票
let limitPrice = 15;  // 最小价格
let price = 100;  // 起始值

while (price > limitPrice) {
    price = priceGenerator.next().value;
    console.log(`${price}`);
}
console.log(`${price}`);
```

- 析构表达式

通过表达式将对象或数组拆解成任意数量的变量

```ts
function getStock() {  // 股票函数
    return {
        code: "IMB",
        price: 100,
        PRICE: {
            price1: 10000
        }
    }
}

let {code, price} = getStock();  // 转换成本地的变量
console.log(code, price);  // IMB 100


let {code: hello, price: world} = getStock();  // 对象取出来的值转换到本地的hello和world变量中
console.log(hello, world);  // IMB 100


let {PRICE: {price1}} = getStock();  // 获取嵌套对象里面的值
console.log(price1);  // 10000
```

从数组里面中取值

```ts
let array1 = [1, 2, 3, 4];
let [number1, number2, ...others] = array1;
console.log(number1, number2);  // 1 2
console.log(others);  // 3 4

let [, , number3, number4] = array1;
console.log(number3, number4);  // 3 4
```

用作函数的参数

```ts
let array1 = [1, 2, 3, 4];

function doSomething([number1, number2, ...others]) {
    console.log(number1);  // 1
    console.log(number2);  // 2
    console.log(others);  // [3, 4]
}
doSomething(array1);
```

## 表达式与循环

- 箭头表达式

用来声明匿名函数，消除传统匿名函数的this指针问题

```js
let sum = (arg1, arg2) => arg1 + arg2; // 单行

let sum1 = (arg1, arg2) => {
    return arg1 + arg2;
}; // 多行

let sum2 = () => {

}; // 没有参数

let sum13 = arg1 => {
    console.log(arg1)
}; // 单个参数
```

实例

```js
let myArray = [1, 2, 3, 4, 5];
console.log(myArray.filter(value => value % 2 == 0));  // 取偶数 [2, 4]
```

- for of循环

```ts
let myArray = [1, 2, 3, 4, 5, 6, 7];
// myArray.desc = 'Hello,World';  // 属性
myArray.forEach(value => console.log(value));  // 不支持属性

for (let n in myArray) {  // 支持属性
    console.log(myArray[n]);
}

for (let n of myArray) {
    if (n == 5) {
        break;  // 执行break
    } else {
        console.log(n)
    }
}
```

## 面向对象

- 类

类是typescript的核心，使用TypeScript开发时，大部分代码都是写在类里面的。

定义类

```ts
class Person {  // 创建类
    name: string;  // 类属性

    eat() {   // 类方法
        console.log('吃');
    }
}
let p1 = new Person();  // 创建类
p1.name = 'Hello';  // 类属性赋值
p1.eat();  // 调用类方法
```

|控制符|描述|
|:--|:--|
|public|类的属性在内部和外部都可以被修改|
|private|私有的属性和方法只有在类的内部访问和修改|
|protected|受保护的属性和方法，在类的内部和子类被访问|

类的构造函数

```ts
class Person {  // 创建类
    constructor(public name: string) { // 构造方法,public name声明name属性
        console.log('Hello, Person');
    }
}
let p1 = new Person('AS');  // 创建类
console.log(p1.name);
```

继承

```ts
class Person {  // 创建类
    constructor(public name: string) { // 构造方法,public name声明name属性
        console.log(1);
    }

    eat() {
        console.log(3);
    }
}

class Employee extends Person {
    constructor(name: string, public code: string) {
        super(name);
        this.code = code;
        console.log(2);
    }

    work() {
        super.eat();
        console.log(4);
        this.doWork();
    }

    private doWork() {  // 只能在内部调用
        console.log(5);
    }
}

let e1 = new Employee('Hello', '110');
e1.work();
```

- 泛型

用来指定数组只能存在的数据类型

```ts
class Person {
    constructor(public name: string) {
        console.log(this.name);
    }
}

let workers: Array<Person> = [];
workers[0] = new Person("Hello");
workers[1] = new Person("World");
// workers[1] = 3;  // 并存放
```

- 接口

用来建立某种代码约定，使得其他开发者在调用某个方法或创建新的类时必须遵循接口所定义的代码约定。


用接口来声明属性

```ts
interface IPerson {  // 声明接口
    name: string;
    age: number;
}

class Person {
    constructor(public config: IPerson) {  // 作为一个方法参数的类型声明
        console.log(config);
    }
}

let p1 = new Person({
    name: 'Hello',
    age: 20
});
```

用接口来声明方法

```ts
interface Animal {
    eat();
}

class Sheep implements Animal {
    eat() {
        console.log('Sheep 吃');
    }
}

class Tiger implements Animal {
    eat() {
        console.log('Tiger 吃')
    }
}
```

- 模块

模块可以帮助开发者将代码分割为可重用的单元，开发者可以自己决定将模块中的那些资源暴露出去供外部使用，那些资源只在模块内使用。

|关键字|描述|
|:--|:--|
|export|导出模块|
|import|导入模块|

导出模块

```ts
export let prop1;
let prop2;
export function func1() {

}

function func2() {

}

export class Class1 {

}
class CLass2 {

}
```

导入模块

```ts
import {Class1, func1, prop1} from "./a";
console.log(prop1);

func1();

new Class1();
```