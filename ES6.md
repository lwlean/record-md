> [引用](http://caibaojian.com/es6/)
# var 、let 、const
- var 全局有效 可以变量提升
- let 声明的变量仅在块级作用域 在声明后使用 否则出现 ReferenceError
- const 声明一个只读的常量。一旦声明，常量的值就不能改变，只在块级作用域内

## 暂时性死区（TDZ）
```javascript
var temp = 'aaa'

if (true) {
    temp = 'bbb';
    let temp; // 后者绑定了变量temp到 该作用域 形成了封闭，所以再此作用域之前使用该变量就会报错
}
```

## 不允许重复声明
- let const 不允许在相同作用域内，重复声明同一变量
- let const不能在函数内部重新声明参数
- var 声明过的也算

## 变量提升

```javascript
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = "hello world"; // 将变量提升到顶部
  }
}

f(); // undefined
```

## 全局变量泄露
```javascript
var s = 'hello';

for (var i = 0; i < s.length; i++) {
  console.log(s[i]); // undefined
}

console.log(i); // 5
```

## 块级作用域
```javascript
function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5
}
```
## 替代立即执行函数 
```javascript
(function(){

})()

{
    let a = 'll'
    console.log(a);
}
```

## 块级作用的一些问题
- 严格模式下ES5不支持在块级作用域内声明函数，为保险起见，避免在块级作用域内声明函数，尽量提前声明，并且最好函数表达式
- ES6的块级作用域允许声明函数的规则，只在使用大括号的情况下成立，如果没有使用大括号，就会报错。

## do 表达式

使得块级作用域可以变为表达式，也就是说可以返回值，办法就是在块级作用域之前加上`do`，使它变为`do`表达式

```javascript
let x = do {
  let t = f();
  t * t + 1;
};
```

## 冻结对象
let const 声明的变量不能重新复制，为了防止该现象，可以使用冻结
```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

可以将对象的属性冻结

```javascript
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, value) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

## 顶层对象

顶层对象属性与全局对象属性挂钩

# 变量的解构赋值

## 数组结构

```javascript
var a = 1;
var b = 2;
var c = 3;
var [a, b, c] = [1, 2, 3];

let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1s
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []

let [x, y, z] = new Set(["a", "b", "c"]); // Set也可以用解构
```

## 结构失败

```javascript
// 报错 如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```

## 生成器函数

它符合[可迭代协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#iterable)和[迭代器协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#iterator)。

- Generator.prototype.next() 返回一个由 yield表达式生成的值。
- Generator.prototype.return() 返回给定的值并结束生成器。
- Generator.prototype.throw() 向生成器抛出一个错误。

```javascript
function* generator(i) {
  yield i;
  yield i + 10;
}

const gen = generator(10);

console.log(gen.next().value);
// expected output: 10

console.log(gen.next().value);
// expected output: 20

```

## 满足具有Iterator接口。解构赋值会依次从这个接口获取

```javascript
function* fibs() {
  var a = 0;
  var b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

var [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```

## 默认值

解构赋值允许指定默认值
```javascript
let [a = 2, b = 3] = [ , 2];
console.log(b); // 2
```

## 对象的解构

对象的属性是没有顺序的，因此对象属性解构必须名称一致

```javascript
var { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

var { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```

变量名和属性名不一致的情况

```javascript
var { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```

因此对象属性解构是以下的简写

```javascript
var { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```

嵌套解构

```javascript
var obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

var { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // undefined
// 这时p是模式，不是变量，因此不会被赋值。
```

对象的解构也可以指定默认值

```javascript
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var {x:y = 3} = {};
y // 3

var {x:y = 3} = {x: 5};
y // 5

var { message: msg = 'Something went wrong' } = {};
msg // "Something went wrong"
```

默认值生效的条件是，对象的属性值严格等于`undefined`

```javascript
var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null 如果x属性等于null，就不严格相等于undefined，导致默认值不会生效
```

## 字符串的解构赋值

```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"

let {length : len} = 'hello';
len // 5
```

## 数值和布尔值的解构赋值

解构赋值时，如果等号右边是数值和布尔值，则会先转为对象

```javascript
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true

let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
// 解构赋值的规则是，只要等号右边的值不是对象，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错
```

## 函数参数的解构赋值

```javascript
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3

[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```

函数参数的解构也可以使用默认值

```javascript
function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```

函数参数解构失败

```javascript
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```

## 圆括号问题

### 不能使用圆括号的情况

以下三种解构赋值不得使用圆括号

- 变量的声明不能带圆括号

```javascript
// 全部报错
var [(a)] = [1];

var {x: (c)} = {};
var ({x: c}) = {};
var {(x: c)} = {};
var {(x): c} = {};

var { o: ({ p: p }) } = { o: { p: 2 } };
```

- 函数参数中，模式不能带有圆括号

```javascript
// 报错
function f([(z)]) { return z; }
```

- 赋值语句中，不能将整个模式，或嵌套模式中的一层，放在圆括号之中。

```javascript
// 全部报错
({ p: a }) = { p: 42 };
([a]) = [5];
```

### 可以使用圆括号的情况

赋值语句的非模式部分，可以使用圆括号

```javascript
[(b)] = [3]; // 正确
({ p: (d) } = {}); // 正确
[(parseInt.prop)] = [3]; // 正确
```

## 用途

### 交换变量

```javascript
[x, y] = [y, x];
```

### 从函数返回多个值

```javascript
// 返回一个数组

function example() {
  return [1, 2, 3];
}
var [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
var { foo, bar } = example();
```

### 函数参数的定义

```javascript
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

### 提取JSON数据

```javascript
var jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```

### 遍历map set解构
```javascript
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
```

### 输入模块的指定方法
```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```

# 字符串的扩展

## codePointAt()

`codePointAt`方法，能够正确处理4个字节储存的字符，返回一个字符的码点

```javascript
var s = "𠮷";

s.length // 2
s.charAt(0) // ''
s.charAt(1) // ''
s.charCodeAt(0) // 55362
s.charCodeAt(1) // 57271
```

```javascript
var s = '𠮷a';

s.codePointAt(0) // 134071
s.codePointAt(1) // 57271

s.codePointAt(2) // 97
```

## at()

提出字符串实例的`at`方法，可以识别Unicode编号大于`0xFFFF`的字符，返回正确的字符

```javascript
'abc'.at(0) // "a"
'𠮷'.at(0) // "𠮷"
```

## includes(), startsWith(), endsWith()

传统上，JavaScript只有`indexOf`方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6又提供了三种新方法

- **includes()**：返回布尔值，表示是否找到了参数字符串。
- **startsWith()**：返回布尔值，表示参数字符串是否在源字符串的头部。
- **endsWith()**：返回布尔值，表示参数字符串是否在源字符串的尾部。

```javascript
var s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

## repeat()

```javascript
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

## padStart()，padEnd()

```javascript
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

## 模板字符串

```javascript
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```

```javascript
function fn() {
  return "Hello World";
}

`foo ${fn()} bar`
// foo Hello World bar 模板字符串之中还能调用函数
```

```javascript
const tmpl = addrs => `
  <table>
  ${addrs.map(addr => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `).join('')}
  </table>
`;
// 模板字符串甚至还能嵌套
```

```javascript
// 写法一
let str = 'return ' + '`Hello ${name}!`';
let func = new Function('name', str);
func('Jack') // "Hello Jack!"

// 写法二
let str = '(name) => `Hello ${name}!`';
let func = eval.call(null, str);
func('Jack') // "Hello Jack!"
```