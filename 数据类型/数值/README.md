## 整数与浮点数

JavaScript 内部，所有数字都是以64位浮点数形式储存，即使整数也是如此。所以，`1`与`1.0`是相同的，是同一个数。

```js
1 === 1.0 // true
```

由于浮点数不是精确的值，所以涉及小数的比较和运算要特别小心

```js
0.1 + 0.2 === 0.3 // false

0.3 / 0.1
// 2.9999999999999996

(0.3 - 0.2) === 0.1
// false
```

## 数值精度

根据国际标准 IEEE 754，JavaScript 浮点数的64个二进制位，从最左边开始，是这样组成的。

- 第1位：符号位，`0`表示正数，`1`表示负数
- 第2位到第12位（共11位）：指数部分
- 第13位到第64位（共52位）：小数部分（即有效数字）

符号位决定了一个数的正负，指数部分决定了数值的大小，小数部分决定了数值的精度。

指数部分一共有11个二进制位，因此大小范围就是0到2047。IEEE 754 规定，如果指数部分的值在0到2047之间（不含两个端点），那么有效数字的第一位默认总是1，不保存在64位浮点数之中。也就是说，有效数字这时总是1.xx...xx的形式，其中xx..xx的部分保存在64位浮点数之中，最长可能为52位。因此，JavaScript 提供的有效数字最长为53个二进制位。

```text
(-1)^符号位 * 1.xx...xx * 2^指数部分
```

精度最多只能到53个二进制位，这意味着，绝对值小于2的53次方的整数，即-253 + 1 到 253 - 1，都可以精确表示。

```js
2 ** 53
// 9007199254740992

2 ** 53 + 1
// 9007199254740992

2 ** 53 + 2
// 9007199254740994

2 ** 53 + 3
// 9007199254740996

2 ** 53 + 4
// 9007199254740996
```

上面代码中，大于2的53次方以后，整数运算的结果开始出现错误。所以，大于2的53次方的数值，都无法保持精度。由于2的53次方是一个16位的十进制数值，所以简单的法则就是，JavaScript 对15位的十进制数都可以精确处理。

```js
2 ** 53
// 9007199254740992

// 多出的三个有效数字，将无法保存
9007199254740992111
// 9007199254740992000
```

上面示例表明，大于2的53次方以后，多出来的有效数字（最后三位的111）都会无法保存，变成0。

## 数值范围

根据标准，64位浮点数的指数部分的长度是11个二进制位，意味着指数部分的最大值是2047（2的11次方减1）。也就是说，64位浮点数的指数部分的值最大为2047，分出一半表示负数，则 JavaScript 能够表示的数值范围为21024到2-1023（开区间），超出这个范围的数无法表示。

如果一个数大于等于2的1024次方，那么就会发生“正向溢出”，即 JavaScript 无法表示这么大的数，这时就会返回Infinity。

```js
2 ** 1024 // Infinity
```

如果一个数小于等于2的-1075次方（指数部分最小值-1023，再加上小数部分的52位），那么就会发生为“负向溢出”，即 JavaScript 无法表示这么小的数，这时会直接返回0。

下面是一个实际的例子。

```js
let x = 0.5

for (let i = 0; i < 25; i++) {
  x = x * x
}

x // 0
```

上面代码中，对`0.5`连续做25次平方，由于最后结果太接近0，超出了可表示的范围，JavaScript 就直接将其转为0。

JavaScript 提供Number对象的MAX_VALUE和MIN_VALUE属性，返回可以表示的具体的最大值和最小值。

JavaScript 提供Number对象的MAX_VALUE和MIN_VALUE属性，返回可以表示的具体的最大值和最小值。

```js
Number.MAX_VALUE // 1.7976931348623157e+308
Number.MIN_VALUE // 5e-324
```

## 数值的表示法

JavaScript 的数值有多种表示方法，可以用字面形式直接表示，比如35（十进制）和0xFF（十六进制）。

数值也可以采用科学计数法表示，下面是几个科学计数法的例子。

```js
123e3 // 123000
123e-3 // 0.123
- 3.1e+12
0.1e-23
```

科学计数法允许字母e或E的后面，跟着一个整数，表示这个数值的指数部分。

以下两种情况，JavaScript 会自动将数值转为科学计数法表示，其他情况都采用字面形式直接表示。

（1）小数点前的数字多于21位

```js
1234567890123456789012
// 1.2345678901234568e+21

123456789012345678901
// 123456789012345680000
```

（2）小数点后的零多于5个

```js
// 小数点后紧跟5个以上的零，
// 就自动转为科学计数法
0.0000003 // 3e-7

// 否则，就保持原来的字面形式
0.000003 // 0.000003
```

## 数值的进制

使用字面量（literal）直接表示一个数值时，JavaScript 对整数提供四种进制的表示方法：十进制、十六进制、八进制、二进制。

- 十进制：没有前导0的数值。
- 八进制：有前缀0o或0O的数值;或者有前导0、且只用到0-7的八个阿拉伯数字的数值，但是在严格模式下，不允许这样做。
- 十六进制：有前缀0x或0X的数值。
- 二进制：有前缀0b或0B的数值。

默认情况下，JavaScript 内部会自动将八进制、十六进制、二进制转为十进制。下面是一些例子。

```js
0xFF // 255
0o377 // 255
0b11 // 3
```

如果八进制、十六进制、二进制的数值里面，出现不属于该进制的数字，就会报错。

```js
0xzz // 报错
0o88 // 报错
0b22 // 报错
```

通常来说，有前导0的数值会被视为八进制，但是如果前导0后面有数字`8`和`9`,则该数被视为十进制

```js
0888 // 888
0777 // 511
```

前导0表示八进制，处理时很容易造成混乱。ES5 的严格模式和 ES6，已经废除了这种表示法，但是浏览器为了兼容以前的代码，目前还继续支持这种表示法。

## 特殊数值

### 正零和负零

前面说过，JavaScript 的64位浮点数之中，有一个二进制位是符号位。这意味着，任何一个数都有一个对应的负值，就连0也不例外。

JavaScript 内部实际上存在2个0：一个是+0，一个是-0，区别就是64位浮点数表示法的符号位不同。它们是等价的。

```js
+0 === -0 // true
- 0 // true
+ 0 === 0 === 0 // true
```

几乎所有场合，正零和负零都会被当作正常的`0`

```js
+0 // 0
- 0 // 0
(-0).toString() // '0'
(+0).toString() // '0'
```

唯一有区别的场合是，`+0`和`-0`被当作分母，返回值是不想等的

```js
(1 / +0) === (1 / -0) // false
```

上面的代码之所以出现这样的结果，是因为除以正零得到`+Infinity`，除以负零得到`-Infinity`，这两者是不想等的。

### NaN

`NaN`是 JavaScript 的特殊值，表示“非数字”（Not a Number），主要出现在将字符串解析成数字出错的场合。

```js
5 - 'x' // NaN
```

另外，一些数学函数的运算结果会出现NaN。

```js
Math.acos(2) // NaN
Math.log(-1) // NaN
Math.sqrt(-1) // NaN
```

`0`除以`0`会得到`NaN`。

```js
0 / 0 // NaN
```

需要注意的是，NaN不是独立的数据类型，而是一个特殊数值，它的数据类型依然属于Number，使用typeof运算符可以看得很清楚。

```js
typeof Number.NaN // 'number'
```

#### NaN的运算规则

`NaN`不等于任何值，包括它本身

```js
Number.NaN === Number.NaN // false
```

数组的`indexOf`方法内部使用的是严格相等运算符，所以该方法对`NaN`不成立。

```js
[Number.NaN].indexOf(Number.NaN) // -1
```

`NaN`在布尔运算时被当作`false`

```js
Boolean(Number.NaN) // false
```

`NaN`与任何数（包括自己）的运算，得到的都是`NaN`

```js
Number.NaN + 32 // NaN
Number.NaN - 32 // NaN
Number.NaN * 32 // NaN
Number.NaN / 32 // NaN
```

### Infinity

Infinity表示“无穷”，用来表示两种场景。一种是一个正的数值太大，或一个负的数值太小，无法表示；另一种是非0数值除以0，得到Infinity。

```js
// 场景一
2 ** 1024
// Infinity

// 场景二
0 / 0 // NaN
1 / 0 // Infinity
```

上面代码中，第一个场景是一个表达式的计算结果太大，超出了能够表示的范围，因此返回`Infinity`。第二个场景是`0`除以`0`会得到`NaN`，而非0数值除以`0`，会返回`Infinity`。

`Infinity`有正负之分，`Infinity`表示正的无穷，`-Infinity`表示负的无穷。

```js
Infinity === -Infinity // false

1 / -0 // -Infinity
- 1 / -0 // Infinity
```

上面代码中，非零正数除以`-0`，会得到`-Infinity`，负数除以`-0`，会得到`Infinity`。

由于数值正向溢出（overflow）、负向溢出（underflow）和被`0`除，JavaScript 都不报错，所以单纯的数学运算几乎没有可能抛出错误。

`Infinity`大于一切数值（除了`NaN`），`-Infinity`小于一切数值（除了`NaN`）。

```js
Infinity > 1000 // true
- Infinity < -1000 // true
```

`Infinity`与`NaN`比较，总是返回`false`

```js
Infinity > Number.NaN // false
- Infinity > Number.NaN // false

Infinity < Number.NaN // false
- Infinity < Number.NaN // true
```

#### Infinity的运算规则

`Infinity`的四则运算，符合无穷的数学计算规则

```js
5 * Infinity // Infinity
5 - Infinity // -Infinity
5 / Infinity // 0
Infinity / 5 // Infinity
```

`0`乘以`Infinity`，返回`NaN`；`0`除以`Infinity`，返回`0`；`Infinity`除以`0`，返回`Infinity`。

```js
0 * Infinity // NaN
0 / Infinity // 0
Infinity / 0 // Infinity
```

`Infinity`加上或乘以自身，得到`Infinity`。

```js
Infinity + Infinity // Infinity
Infinity * Infinity // Infinity
```

`Infinity`减去或除以自身，得到`NaN`。

```js
Infinity - Infinity // NaN
Infinity / Infinity // NaN
```

`Infinity`与`null`计算时，`null`会转成0，等同于与`0`的计算。

```js
null * Infinity // 0
null / Infinity // 0
Infinity / null // Infinity
```

`Infinity`与`undefined`计算，返回都是`NaN`。

```js
undefined * Infinity // NaN
undefined / Infinity // NaN
undefined + Infinity // NaN
undefined - Infinity // NaN
Infinity / undefined // NaN
```

## 数值相关的全局方法
这些方法都是全局方法，调用时不需要指定对象，直接调用就可以

### parseInt()

`parseInt`方法用于将字符串转为整数

```js
Number.parseInt('123') // 123
```

如果字符串头部或者尾部有空格，空格会被自动去除

```js
Number.parseInt('    123') // 123
```

如果`parseInt`的参数不是字符串，则会先转为字符串再转换

```js
Number.parseInt(1.23) // 1
// 等同于
Number.parseInt('1.23') // 1
```

`parseInt`可以接受第二个参数（2到36之间），用于指定进制，返回该值对应的十进制数。默认是十进制。

```js
Number.parseInt('1000') // 1000
// 等同于
Number.parseInt('1000', 10) // 1000
```

下面是一些例子。

```js
Number.parseInt('10', 2) // 2
Number.parseInt('10', 8) // 8
Number.parseInt('10', 16) // 16
```

如果第二个参数不是数值，会被自动转为一个整数。这个整数只有在 2 到 36 之间，才能得到有意义的结果，超出这个范围，则返回 `NaN`。如果第二个参数是 `0`、`undefined` 和 `null`，则直接忽略。

```js
Number.parseInt('10', 37) // NaN
Number.parseInt('10', 1) // NaN
Number.parseInt('10', 0) // 10
Number.parseInt('10', null) // 10
Number.parseInt('10', undefined) // 10
```

JavaScript 不再允许将带有前缀 0 的数字视为八进制数，而是要求忽略这个 0。但是，为了保证兼容性，大部分浏览器并没有部署这一条规定。（NodeJS中严格模式不允许出现八进制的字面量）

### parseFloat()

`parseFloat`方法用于将一个字符串转为浮点数

```js
Number.parseFloat('3.14') // 3.14
```

`parseFloat`方法会自动过滤字符串前导的空格

```js
Number.parseFloat('\t\v\r12.34\n ') // 12.34
```

它与`Number`函数的一个区别：
```js
Number.parseFloat(true) // NaN
Number(true) // 1

Number.parseFloat(null) // NaN
Number(null) // 0

Number.parseFloat('') // NaN
Number('') // 0

Number.parseFloat('123.45#') // 123.45
Number('123.45#') // NaN
```

### isNaN()
`isNaN`方法可以用来判断一个值是否为NaN

但是，`isNaN`只对数值有效，如果传入其他值，会被先转成数值
```
isNaN('Hello') // true
// 等同于
isNaN(Number('Hello')) // true
```

判断NaN可靠的方法是，利用NaN为唯一不等于自身的值的这个特点，进行判断

```js
function myIsNaN(value) {
  return value !== value
}
```

### isFinite

`isFinite`方法返回一个布尔值，表示某个值是否为正常的数值

除了`Infinity`、`-Infinity`、`NaN`和`undefined`这几个值会返回`false`，`isFinite`对于其他的数值都会返回`true`

```js
isFinite(Infinity) // false
isFinite(-Infinity) // false
isFinite(Number.NaN) // false
isFinite(undefined) // false
isFinite(null) // true
isFinite(-1) // true
```

<br>
<a href="../../README.md"><img src="https://img.shields.io/badge/-%E8%BF%94%E5%9B%9E%E9%A6%96%E9%A1%B5-grey" alt="返回首页"/></a>
