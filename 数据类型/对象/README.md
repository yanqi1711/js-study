## 概述

什么是对象？简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合

对象的每一个键名又称为“属性”（property），它的“键值”可以是任何数据类型。如果一个属性的值为函数，通常把这个属性称为“方法”

```js
const obj = {
  p(x) {
    return 2 * x
  }
  // 等同于
  // p: function(x) {
  //   return 2 * x
  // }
}

obj.p(1) // 2
```

如果属性的值还是一个对象，就形成了链式引用

```js
const o1 = {}
const o2 = { a: 'hello', b: 'world' }

// 属性可以动态创建
o1.foo = o2
o1.foo.a // "hello"
o1.foo.b // "world"
```

## 对象的引用

如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有变量。（这种引用仅限于对象）

```js
const a = {}
const b = a
a.a = 1
console.log(b.a) // 1
b.a = 2
console.log(a.a) // 2
```

## 表达式和代码块

为了避免歧义，无法确定是对象还是代码块时，JavaScript 引擎会将以大括号开头的语句块解释为代码块

```js
{ console.log(123) } // 123
```

如果要解释为对象，可以在大括号前加上圆括号

```js
({ foo: 123 }) // 正确
({ console.log(123) }) // 报错
```

## 属性的操作
### 属性的读取

一种是使用点运算符（推荐），还有一种是使用方括号运算符（不推荐）

### 属性的查看

查看一个对象本身的所有属性，可以使用Object.keys方法

```js
const obj = {
  key1: 1,
  key2: 2
}

Object.keys(obj)
// ['key1', 'key2']
```

### 属性的删除

`delete`命令用于删除对象的属性，删除成功后返回`true`

```js
const obj = { p: 1 }
Object.keys(obj) // ["p"]

delete obj.p // true
Object.keys(obj) // []
```

注意，删除一个不存在的属性，`delete`不报错，而且返回`true`

只有一种情况，`delete`命令会返回`false`，那就是该属性存在，且不得删除

```js
const obj = Object.defineProperty({}, 'p', {
  value: 123,
  configurable: false
})

obj.p // 123
delete obj.p // false
```

另外，需要注意的是，`delete`命令只能删除对象本身的属性，无法删除继承的属性

```js
const obj = {}
delete obj.toString // true
obj.toString // function toString() { [native code] }
```

### 属性是否存在

`in`运算符用于检查对象是否包含某个属性（注意，检查的是键名，不是键值），如果包含就返回true，否则返回false

```js
const obj = { p: 1 }
'p' in obj // true
'toString' in obj // true
```

使用`hasOwnProperty`方法可以检查一个属性是存在于对象本身，还是存在于原型链上

```js
const obj = {}
if ('toString' in obj) {
  console.log(obj.hasOwnProperty('toString')) // false
}
```

### 属性的遍历

对象的属性遍历有多种方法，最常用的是`for...in`循环

```js
const obj = { a: 1, b: 2, c: 3 }

for (const key in obj) {
  console.log(key, obj[key])
}
// a 1
// b 2
// c 3
```

`for...in`循环有两个使用注意点。

- 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
- 它不仅遍历对象自身的属性，还遍历继承的属性。

<br>
<a href="../../README.md"><img src="https://img.shields.io/badge/-%E8%BF%94%E5%9B%9E%E9%A6%96%E9%A1%B5-grey" alt="返回首页"/></a>
