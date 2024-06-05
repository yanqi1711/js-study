## null和undefined

### 含义

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

