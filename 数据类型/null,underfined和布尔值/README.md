## null和undefined

### 探讨

`null`和`undefined`都表示没有,含义非常相近.
他们在转换成布尔类型的时候都自动表示为`false`,当使用`==`来运算时,的返回值为`true`
```js
if (!undefined) {
  console.log('undefined is false')
}
// undefined is false

if (!null) {
  console.log('null is false')
}
// null is false

undefined == null
// true
```

### 数值转换

`null`当转换成数值类型的时候会变成`0`;
而`undefined`则是`NaN`
```js
Number(null) // 0
Number(undefined) // NaN
```

### 使用场景

<samp><b>null</b></samp>
调用函数时，某个参数未设置任何值，这时就会可以传入`null`，表示该参数为空

<samp><b>undefined</b></samp>
典型场景:
```js
// 变量声明了，但没有赋值
var i;
i // undefined

// 调用函数时，应该提供的参数没有提供，该参数等于 undefined
function f(x) {
  return x;
}
f() // undefined

// 对象没有赋值的属性
var  o = new Object();
o.p // undefined

// 函数没有返回值时，默认返回 undefined
function f() {}
f() // undefined
```

## 布尔值

布尔值只有`true`和`false`这两个值

如果JS中预期某个位置应该是布尔值，会将该位置上现有的值自动转为布尔值。转换规则是除了下面六个值被转为`false`，其他都视为`true`

- `undefined`
- `null`
- `false`
- `0`
- NaN
- `''`或者`""` (空字符串)

注意: 空数组和空对象的对应布尔值都是`true`


<br>
<a href="../../README.md"><img src="https://img.shields.io/badge/-%E8%BF%94%E5%9B%9E%E9%A6%96%E9%A1%B5-grey" alt="返回首页"/></a>
