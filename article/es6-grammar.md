# ES6语法小记

## let

- 语法

```js
let 变量名称 = 值;
```

- 特点

1. 只在当前代码块中生效；
2. `let`声明的变量不能重复定义；

- 示例

```js
let name = "ansheng";
```

## const

声明常量

- 语法

```js
const 常量名称 = 值;
```

- 特点

1. 拥有`let`所有的功能；
2. 值不可以被修改；
3. 常量名应为全大写；
4. 声明常量时必须赋值;

- 示例

```js
const AGE = 18;
```

## 解构赋值

- 列表解构赋值

```js
let a, b, rest;
[a, b] = [1, 2]
console.log(a, b);  // 1 2
```

```js
let a, b, rest;
[a, b, ...rest] = [1,2,3,4,5,6];
console.log(a, b, rest);  // 1 2 [3, 4, 5, 6]
```

选择性的接受某几个变量

```js
function f(){
    return [1, 2, 3, 4, 5];
}
let a, b, c;
[a,,,,b] = f();
console.log(a, b);  // 1 5
```

- 对象解构赋值

```js
let a,b;
({a,b} = {a:1,b:2})
console.log(a,b);  // 1 2
```

```js
let o = {p:42, q:true};
let {p, q} = o;
console.log(p, q);  // 42 true
```

嵌套对象和数组解构赋值

```js
let metaData = {
    title: 'abc',
    test: [{
        title: 'test',
        desc: 'description'
    }]
}

let {title:esTitle, test:[{title:cnTiele}]} = metaData;
console.log(esTitle,cnTiele);  // abc test
```

- 默认值

定义变量a、b、c

```js
let a, b, c;
```

指定默认值

```js
[a, b, c=3] = [1, 2]
```

如果没有指定默认值，c则为`undefined`

```js
[a, b, c] = [1, 2]
```

- 变量交换

```js
let a = 1;
let b = 2;
console.log(a, b);  // 1 2
[a, b] = [b, a];
console.log(a, b);  // 2 1
```

- 函数解构赋值

```js
function f(){
    return [1, 2];
}
let [a, b] = f();
console.log(a, b);  // 1 2
```

## 字符串扩展

### Unicode表示法

- 常规表示法

```js
console.log('a',`\u0061`)  // a a
```

- ES6表示法

```js
console.log('s',`\u{20BB7}`);  // s 𠮷
```

### 遍历接口

```js
let str = '\u{20bb7}abc';
for (let code of str) {
    console.log(code);
}
```

### 模板字符串

```js
let name = 'list';
let info = 'hello world';
let template = `i am ${name}, ${info}!`;

console.log(template)  // i am list, hello world!
```

模板字符串中的花括号支持表达式

```js
console.log(`1 + 1 = ${1 + 1}`)  // 1 + 1 = 2
```

- 标签模板

```js
let user = {
    name: 'list',
    info: 'hello world'
};

console.log(abc`i am ${user.name}, ${user.info}`)  // i am ,, ,listhello world

function abc(s, v1, v2){
    // ["i am ", ", ", "", raw: Array(3)]
    // "list"
    // "hello world"
    console.log(s, v1, v2)
    return s + v1 + v2;
}
```

### 新增方法

- 获取字符的码值

```js
let s = '𠮷a';

console.log('code0', s.codePointAt(0));  // code0 134071
console.log('code0', s.codePointAt(0).toString(16));  // 转换为十六进制  // code0 20bb7
```

- 根据码值获取对应的字符

```js
console.log(String.fromCodePoint("0x20bb7"));  // 𠮷
```

- 字符串中是否包含某个字符

```js
let str = 'string';
console.log("includes", str.includes("r"))  // includes true
```

- 字符串是否以某些字符为起始

```js
let str = 'string';
console.log("start", str.startsWith("str"))  // start true
```

- 字符串是否以某些字符为结束

```js
let str = 'string';
console.log("end", str.endsWith("ng"))  // end true
```

- 指定字符串重复的次数

```js
let str = 'abc';
console.log(str.repeat(2)) // abcabc
```

- 返回字符串指定的长度，如果长度不够则向前用`0`补齐

```js
console.log('1'.padStart(2, '0'))  // 01
```

- 返回字符串指定的长度，如果长度不够则向后用`0`补齐

```js
console.log('1'.padEnd(2, '0'))  // 10
```

- 输出原生字符

```js
console.log(String.raw`Hi\n ${1+2}`);  // Hi\n 3
console.log(`Hi\n ${1+2}`);
// Hi
//  3
```

## 数值扩展

- 二进制的表示方法

```js
console.log(0b111110111);  // 503
```

- 八进制的表示方法

```js
console.log(0o767);  // 503
```

- 判断值是否有尽

```js
console.log('15', Number.isFinite(15));  // 15 true
console.log('NaN', Number.isFinite(NaN));  // NaN false
console.log('1/0', Number.isFinite('true'/0));  // 1/0 false
```

- 判断值是不不是数字类型

```js
console.log('Nan', Number.isNaN(NaN));  // Nan true
console.log('0', Number.isNaN(0));  // 0 false
```

- 判断值是不是整数

```js
console.log('25', Number.isInteger(25));  // 0 true
console.log('25.0', Number.isInteger(25.0));  // 25.0 true
console.log('25.5', Number.isInteger(25.5));  // 25.0 true
```

- 判断一个数是否在有效的范围内

```js
console.log('10', Number.isSafeInteger(10));  // 10 true
console.log('9007199254740999', Number.isSafeInteger(9007199254740999));  // 9007199254740999 false
```

- 返回一个小数的整数部分

```js
console.log(4.1, Math.trunc(4.1));  // 4.1 4
console.log(4.9, Math.trunc(4.9));  // 4.9 4
```

- 判断一个数是正数、负数还是0

```js
console.log(-5, Math.sign(-5));  // -5 -1
console.log(0, Math.sign(0));  // 0 0
console.log(5, Math.sign(5));  // 5 1
```

- 立方根

```js
console.log(-1, Math.cbrt(-1));  // -1 -1
console.log(8, Math.cbrt(8));  // 8 2
```

- 整数的有效范围

```js
console.log(Number.MAX_SAFE_INTEGER);  // 9007199254740991
console.log(Number.MIN_SAFE_INTEGER);  // -9007199254740991
```

## 数组扩展

- Array.from

把集合或者伪数组转化为真正的数组

```js
console.log(Array.from([1,3,5], function(item){return item * 2})) // [2, 6, 10]
```

- Array.of

```js
let arr = Array.of(3, 4, 7, 9, 11);
console.log(arr);  // [3, 4, 7, 9, 11]

let empty = Array.of()
console.log(empty)  // []
```

- copyWithin

```js
console.log([1,2,3,4,5].copyWithin(0,3,4));  // [4, 2, 3, 4, 5]
```

- find\findIndex

查找item大于3的元素

```js
console.log([1,2,3,4,5,5,6].find(function(item){return item>3}));  // 4
```

查找item大于3的元素的索引

```js
console.log([1,2,3,4,5,5,6].findIndex(function(item){return item>3}));  // 4
```

- fill

填充数组

```js
console.log('fill-7',[1,'a',undefined].fill(7));  
// fill-7 [7, 7, 7]
```
将1-3的位置值替换为7
```js
console.log(['a','b','c'].fill(7,1,3));  // ["a", 7, 7]
 ```

- entries\keys\values

返回数组的下标

```js
for(let index of ['1','c','ks'].keys()){
    console.log(index)
}
// 0
// 1
// 2
```

返回数组的值

```js
for(let value of ['1','c','ks'].values()){
    console.log(value)
}
// 1
// c
// ks
```

返回索引和值

```js
for(let [index, value] of ['1','c','ks'].entries()){
    console.log(index, value)
}
// 0 "1"
// 1 "c"
// 2 "ks"
```

- inludes

数组中是否存在某个元素

```js
console.log([1, 2, NaN].includes(1));  // true
console.log([1, 2, NaN].includes(10));  // false
```

## 函数扩展

- 参数默认值

```js
function test(x, y="world"){
    console.log(x, y)  // undefined "world"
}
test()
```

- rest参数

```js
function test(...args){
    for(let v of args){
        console.log("rest", v)
    }
}
test(1, 2, 3, 4, 5)
```

- 箭头函数

语法：

```js
let 函数名 = 参数 => 函数体;
let func = () => 5;  // 空参数，返回值是5
```

定义一个函数`arrow`，接受一个参数`v`，然后把`v*2`进行返回

```js
let arrow=v=>v*2;
console.log(arrow(2))  // 4
```

- 尾调用

```js
function tail(x){
    console.log("tail",x);  // tail JS
}

function fx(x){
    return tail(x)  // 一个函数返回另外一个函数的调用
}

fx("JS")
```

## Object扩展

- 简介表示法

```js
let o = 1;
let k = 2;
let obj = {
    o,
    k
}
console.log(obj)
// {o: 1, k: 2}
```

```js
let obj = {
    hello(){
        console.log("Hello, World!")
    }
}
obj.hello()
// Hello, World!
```

- 属性表达式

属性的KEY可以用表达式或者变量来表示

```js
let a = 'b';
let obj = {
    [a]:'c'
}
console.log(obj)  // {b: "c"}
```

### Object新增方法

- 判断两个字符串是否相等

```js
console.log('字符串', Object.is('abc','abc'))  // 字符串 true
console.log('数组', Object.is([1,2,3],[1,2,3]),[]===[])  // 数组 false false
```

`Object.is`的判断和`===`等号是没有区别的

- 拷贝

浅拷贝

```js
console.log('拷贝', Object.assign({a:'a'},{b:'b'})); // 拷贝 {a: "a", b: "b"}
```

- entries

```js
let test={k:123,o:456};
for(let [key,value] of Object.entries(test)){
    console.log(key,value)
}
// k 123
// o 456
```

- values

```js
for(let key of Object.keys(test)){
    console.log(key)
}
// k
// o
```

- keys

```js
for(let value of Object.values(test)){
    console.log(value)
}
// 123
// 456
```

- 扩展运算符

```js
let {a,b,...c}={a:'test',b:'kill',c:'add',d:'ccc'};
console.log(a, b, c)  // test kill {c: "add", d: "ccc"}
```

## Symbol

声明的数据类型的值不相等，以保证值是唯一的

- 声明

```js
let a1 = Symbol();
let a2 = Symbol();
console.log(a1===a2)  // false
```

- for

如果在全局中有注册过a3这个变量，则拿过来直接用，否则调用Symbol()重新生成一个

```js
let a3 = Symbol.for('a3');
let a4 = Symbol.for('a3');
console.log(a3 === a4)  // true
```
- 对象key

```js
let a1 = Symbol.for('abc');
let obj = {
    [a1]: '123',
    'abc': 345,
}
console.log(obj)  // {abc: 345, c: 456, Symbol(abc): "123"}  两个KEY值都为abc

for (let [key,value] of Object.entries(obj)) {
    console.log("let of", key, value);
}
```

- 获取对象中Symbols类型的key

```js
Object.getOwnPropertySymbols(obj).forEach((item)=>{
    console.log("getOwnPropertySymbols", item);
})
```

- 获取所有对象中所有的keys

```js
Reflect.ownKeys(obj).forEach((item)=>{
    console.log("ownKeys", item)
})
```

## 数据结构

### Set

- 创建Set

```js
let list = new Set();
```

- 往Set里面添加值

```js
list.add(5);
list.add(7);
```

- 删除Set内的元素

```js
list.delete(7);
console.log(list);  // Set(1) {5}
```

- 获取Set内有多少元素

```js
console.log(list.size);  // 5
```

- 判断Set内是否有某个元素

```js
console.log(list.has(5));  // true
```

- 清空Set内的元素

```js
list.clear();
console.log(list);  // Set(0) {}
```

- 遍历Set

```js
let l = [1, 2, 3, 4, 5];
let s = new Set(l);
console.log(s);  // Set(5) {1, 2, 3, 4, 5}
for(let key of s) {  // of
    console.log(key)
}
// forEach遍历
s.forEach((item)=>{console.log(item)});
```

### WeakSet

WeakSet和Set支持的数据类型不一样

1. WeakSet的元素只能是对象
2. WeakSet的元素是弱引用（地址引用）
3. WeakSet没有clear方法和size属性，也不支持遍历

```js
// 创建
let ws = new  WeakSet();
let arg = {};
// 添加值
ws.add(arg);
console.log(ws);  
```

### Map

```js
// 第一种不带参数的定义方法
let map = new Map();
let arr = ["123"];

// 添加元素
map.set(arr, 456);  // Key, Value
console.log(map);  // Map(1) {Array(1) => 456}

// 获取Map的属性
console.log(map.get(arr));  // 456

// Map的第二种定义方式
let map2 = new Map([['a',123], ['b',456]])
console.log(map2)  // Map(2) {"a" => 123, "b" => 456}

// 查看Map有多少个元素
console.log(map2.size);  // 2

// 删除一个元素
map2.delete("a");
console.log(map2);  // Map(1) {"b" => 456}

// 清空元素
map2.clear();
console.log(map2);  // Map(0) {}

// 遍历和Set一样
```

### WeakMap

```js
let wm = new WeakMap();
// 特性和SetMap一样
```

## 数据结构的横向对比

### Map和Array

- 定义一个Map和Array

```js
let map = new Map();
let array = [];
```

- 增

```js
map.set("t", 1);
array.push({t: 1});
console.info(map, array);  // Map(1) {"t" => 1} [{…}]
```

- 查

```js
let map_exist = map.has("t");
let array_exist = array.fill(item => item.t);
console.info(map_exist, array_exist);  // true [ƒ]
```

- 改

```js
map.set("t", 2);
array.forEach(item => item.t ? item.t = 2: '');
console.info(map, array);  // Map(1) {"t" => 2} [ƒ]
```

- 删

```js
map.delete("t");
let index = array.findIndex(item=>item.t);
array.splice(index,1);
console.info(map, array);  // Map(0) {} []
```

### Set和Array

- 定义

```js
let set = new Set();
let array = [];
```

- 增

```js
let item = {t:1}
set.add(item);
array.push({t:1})
console.log(set, array)  // {{…}} [{…}]
```

- 查

```js
let set_exist = set.has(item);
let array_exist = array.fill(item => item.t);
console.log(set_exist, array_exist);  // true [ƒ]
```

- 改

```js
set.forEach(item => item.t ? item.t = 2: '');
array.forEach(item => item.t ? item.t = 2: '');
console.log(set, array)
```

- 删

```js
set.forEach(item=>item.t?set.delete(item): '');
let index = array.findIndex(item=>item.t);
array.splice(index,1);
console.log(set, array);  // {} []
```

### Map/Set/Object对比

- 定义

```js
let item = {t:1};
let map = new Map();
let set = new Set();
let obj = {};
```

- 增

```js
map.set("t", 1);
set.add(item);
obj["t"] = 1;
console.log(obj,map,set);
```

- 查

```js
let map_exist = map.has("t");
let set_exist = set.has(item);
let obj_exist = "t" in obj;
console.log(map_exist, set_exist, obj_exist);  // true true true
```

- 改

```js
map.set("t", 2);
item.t = 2;
obj["t"] = 2;
```

- 删

```js
map.delete("t");
set.delete(item);
delete obj['t'];
console.log(map, set, obj);  // {} {} {}
```

## 类

### 基本语法

-  定义类

```js
class Parent{
    constructor(name="imooc"){  // 构造方法，接受一个参数name，默认值为imooc
        this.name = name;  // 给类添加一个属性name
    }
}
```

-  生成类实例

```js
let v_parent = new Parent("Hello");
console.log(v_parent.name)  // Hello
```
### 类的继承

```js
class Child extends Parent{
    constructor(name='child') {  // 重写父类的构造方法
        super(name);  // 调用父类的构造方法并把name传递进去
    }
}

let v_child = new Child();
console.log(v_child.name)  // child
```

### getter/setter

```js
class MyCls{
    constructor(name="imooc"){
        this.name = name;
    }
    get longName(){  // 获取属性
        return "mk" + this.name
    }
    set longName(value){  // 设置属性
        this.name=value;
    }
}

let cls = new MyCls();
console.log(cls.longName)  // mkimooc
cls.longName = "Cls"
console.log(cls.longName)  // mkCls
```

### 静态方法

```js
class MyCls2{
    static tell() {  // 静态方法
        console.log("tel")
    }
}

MyCls2.tell()  // 调用静态方法
```

### 静态属性

```js
class MyCls3 {}
MyCls3.type = "test";  // 定义静态属性
console.log(MyCls3.type);  // test
```
